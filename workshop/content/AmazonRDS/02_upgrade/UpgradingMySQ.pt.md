---
title: "Atualizando a versão do banco de dados MySQL do RDS"
date: 2020-04-10T11:14:51-06:00
weight: 30
---

Quando o Amazon RDS começar a oferecer suporte a uma nova versão de um mecanismo de banco de dados, você pode atualizar suas instâncias de banco de dados para a nova versão. 
Existem dois tipos de upgrades para instâncias de banco de dados MySQL: atualizações de versões principais e atualizações de versão secundária. Os principais upgrades de versão podem conter alterações de banco de dados que não são compatíveis com aplicativos existentes. 
Como resultado, você deve executar manualmente atualizações de versões principais de suas instâncias de banco de dados. 
Você pode iniciar uma atualização de versão principal modificando sua instância de banco de dados. Neste exercício, você percorrerá as etapas de atualização do seu mecanismo MySQL para sua instância do RDS. Realizaremos essas operações a partir do Cloud9 usando a AWS CLI, no entanto, você pode obter os mesmos resultados usando o Console da AWS ou modificar a chamada de API de instância db-instância.

{{% notice warning %}} 
Recomendamos que você teste quaisquer alterações em uma instância de teste antes de modificar uma instância de produção, para que você entenda completamente o impacto de cada alteração.
{{% /notice %}}


1. Inicie o processo de atualização da versão principal executando o seguinte comando em seu ambiente Cloud9 :
 
```
aws rds modify-db-instance --db-instance-identifier `aws cloudformation describe-stacks --stack-name GravitonID-rds-5 --query "Stacks[0].Outputs[1].OutputValue" --output text` --engine-version 8.0.21 --allow-major-version-upgrade  --apply-immediately
```

2. Você pode monitorar o processo de atualização de banco de dados usando o AWS Console ou a CLI abaixo
```bash 
aws rds describe-db-instances --db-instance-identifier $DBID | jq -r .DBInstances[0].DBInstanceStatus
```

3. Quando o processo de atualização terminar e seu banco de dados estiver em “available”, conecte-se à sua instância do MySQL atualizada executando o seguinte comando:

```bash
mysql -h $DBHOST -P $DBPORT -u$DBUSER -p$DBPASS
```


4. Verifique a soma de verificação da tabela de funcionários na instância Graviton2 executando:

```bash
use employees; checksum table employees;
```

5. Você deve ver a saída semelhante a uma abaixo. Por favor, compare o valor da soma de verificação com um do banco de dados original.

```
+---------------------+-----------+
| Table               | Checksum  |
+---------------------+-----------+
| employees.employees | 610052939 |
+---------------------+-----------+
1 row in set (0.24 sec)
```

6. Sair do cliente MySQL.
