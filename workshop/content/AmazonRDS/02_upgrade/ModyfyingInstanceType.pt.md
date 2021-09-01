---
title: "Alterando instância para Graviton2"
date: 2020-04-10T11:14:51-06:00
weight: 40
---

Você pode alterar as configurações de uma instância de banco de dados para realizar tarefas como adicionar armazenamento adicional ou alterar a classe da instância de banco de dados. 

Isso inclui switchin de instâncias x86-64 para o tipo de instância Graviton2. 

Recomendamos que você teste quaisquer alterações em uma instância de teste antes de modificar uma instância de produção, para que você entenda completamente o impacto de cada alteração. 

Como mencionado no capítulo anterior, o teste é especialmente importante ao atualizar as versões do banco de dados. 

A maioria das modificações em uma instância de banco de dados você pode aplicar imediatamente ou adiar até a próxima janela de manutenção.


1. Inicie o processo de alteração de tipo de instância executando o seguinte comando em seu ambiente Cloud9 :
 
```

aws rds modify-db-instance --db-instance-identifier `aws cloudformation describe-stacks --stack-name GravitonID-rds-5 --query "Stacks[0].Outputs[1].OutputValue" --output text` --db-instance-class db.m6g.4xlarge --allow-major-version-upgrade --apply-immediately

```

2. Você pode monitorar o processo de atualização de banco de dados usando o AWS Console ou a CLI abaixo
```bash 
aws rds describe-db-instances --db-instance-identifier $DBID | jq -r .DBInstances[0].DBInstanceStatus
```
 
3. Quando o processo de atualização terminar e seu banco de dados estiver em “Availabe”, conecte-se à sua instância do MySQL atualizada executando o seguinte comando:

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

6. Sair do cliente MySQL




Consulte https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.DBInstance.Modifying.html para obter mais detalhes sobre como modificar instâncias de banco de dados do RDS.
