---
title: "AmazonEKS : Implante um aplicativo de várias arquiteturas."
date: 2020-04-10T11:14:51-06:00
weight: 40
---

Agora você está pronto para implantar um aplicativo Kubernetes multiarquitetura.

1. Comece analisando o conteúdo do manifesto de implantação do Kubernetes no arquivo graviton2-workshop/graviton2/cs_graviton/deployment.yaml

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: multiarch-deployment
  namespace: multiarch
  labels:
    app: multiarch-app
spec:
  replicas: 4
  selector:
    matchLabels:
      app: multiarch-app
  template:
    metadata:
      labels:
        app: multiarch-app
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: beta.kubernetes.io/arch
                operator: In
                values:
                - amd64
                - arm64
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - multiarch-app
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: multiarch-container
        image: {{container_uri}} 
        ports:
        - containerPort: 80
```

Este manifesto implantará quatro pods em todos os nós do EKS (amd64 e arm64).

2. Use o seguinte comando para iniciar a implantação :

```bash
cd ~/environment/graviton2-workshop
CONTAINER_URI=`aws ssm get-parameter --name "graviton_lab_container_uri" | jq -r .Parameter.Value`
cat graviton2/cs_graviton/Deployment.yaml | sed "s#{{container_uri}}#$CONTAINER_URI#" | kubectl apply -f -
```

3. Você pode verificar o status de sua implantação executando:

```bash
kubectl get svc,deployment -n multiarch
```

4. Acesse o aplicativo de várias arquiteturas via endpoint LoadBalancer.

{{% notice tip %}} 
Levará vários minutos para que o ELB se torne saudável e comece a passar o tráfego para os pods.{{% /notice %}}

```bash
ELB=$(kubectl get service multiarch-service -n multiarch -o json | jq -r '.status.loadBalancer.ingress[].hostname')
for i in {1..8}; do curl -m3 $ELB ; echo; done
```
{{% notice tip %}} 
Você também pode copiar/colar o nome do host LoadBalancer em seu navegador e ver o aplicativo em execução.{{% /notice %}}

{{% notice note %}}
Observe que estamos usando uma única tag de imagem com o Amazon ECR e o Amazon EKS. O suporte multiarquitetura nativo permite automaticamente selecionar o contêiner certo para cada um dos nodegroups.
{{% /notice %}}

### Parabéns! Agora você implantou o aplicativo de várias arquiteturas em dois grupos de nodegroups usando tipos de instância x86-64 e arm64.


Opcional: Você pode verificar um cenário de falha forçando o uso de uma única imagem de encaixe de arquitetura. Comece diminuindo a implantação, alterando a tag de imagem para apontar para a compilação específica da arquitetura e escalando a implantação de backup para o número original de réplicas.

```bash
kubectl -n multiarch scale deployment multiarch-deployment --replicas=0
kubectl -n multiarch set image deployment/multiarch-deployment multiarch-container=$CONTAINER_URI-x86
kubectl -n multiarch scale deployment multiarch-deployment  --replicas=4
```

Após a estabilização da implantação, execute o comando follwoing verificar o status de implantação:

```bash 
kubectl get pod -n multiarch 
```

Você deve ser capaz de ver a saída semelhante à abaixo onde apenas dois pods relatam “Running”, enquanto dois mostram o status “CrashLoopBackOff” ou “Error”.

```bash
NAME                                    READY   STATUS             RESTARTS   AGE
multiarch-deployment-857dbcdd7c-5455t   0/1     CrashLoopBackOff   6          9m33s
multiarch-deployment-857dbcdd7c-l48zd   1/1     Running            0          9m33s
multiarch-deployment-857dbcdd7c-ptf65   1/1     Running            0          9m33s
multiarch-deployment-857dbcdd7c-xk8vh   0/1     CrashLoopBackOff   6          9m33s
```










