---
title: "Restaurar o snapshot para a instância do Graviton2"
date: 2020-04-10T11:14:51-06:00
weight: 30
---


Estamos prontos para restaurar nosso banco de dados para uma nova instância baseada em Graviton2. Comece revisando o conteúdo da definição de banco de dados RDS MySQL 8 no arquivo graviton2-workshop/graviton2/rds_graviton/ds_restore.py

```
        snapshot_id = ssm.StringParameter.value_for_string_parameter(self ,"graviton_rds_lab_snapshot")
        g2_db_mysql8 = rds.DatabaseInstanceFromSnapshot(self, "GravitonMySQL",
                                             engine=rds.DatabaseInstanceEngine.mysql(
                                                 version=rds.MysqlEngineVersion.VER_8_0_21
                                             ),
                                             instance_type=ec2.InstanceType("m6g.4xlarge"),
                                             snapshot_identifier=snapshot_id,
                                             vpc=vpc,
                                             multi_az=False,
                                             publicly_accessible=True,
                                             allocated_storage=100,
                                             storage_type=rds.StorageType.GP2,
                                             cloudwatch_logs_exports=["error", "general", "slowquery"],
                                             enable_performance_insights=True,
                                             deletion_protection=False,
                                             delete_automated_backups=True,
                                             backup_retention=core.Duration.days(1),
                                             vpc_subnets={
                                                 "subnet_type": ec2.SubnetType.PUBLIC
                                             },
                                             parameter_group=rds.ParameterGroup.from_parameter_group_name(
                                                 self, "para-group-mysql",
                                                 parameter_group_name="default.mysql8.0"
                                             )
                                             )
```
1. Usaremos o snapshot que criamos a partir da instância x86-64 original para restaurar o novo banco de dados Graviton2. Execute o seguinte comando para criar uma nova instância do Graviton2:

```bash

cdk deploy GravitonID-rds-restore
```

2. Antes de nos conectarmos à instância do Graviton2 MySQL, precisamos atualizar o nome de usuário e a senha como variáveis de ambiente em nossa sessão do Cloud9. Usaremos a saída do CloudFormation para encontrar as variáveis relevantes.

```bash
CREDS=$(aws secretsmanager get-secret-value --secret-id `aws cloudformation describe-stacks --stack-name GravitonID-rds-8 --query "Stacks[0].Outputs[0].OutputValue" --output text` | jq -r '.SecretString')
export DBUSER="`echo $CREDS | jq -r '.username'`"
export DBPASS="`echo $CREDS | jq -r '.password'`"
export DBPORT="`echo $CREDS | jq -r '.port'`"
export DBHOST=$(aws rds describe-db-instances --db-instance-identifier `aws cloudformation describe-stacks --stack-name GravitonID-rds-restore --query "Stacks[0].Outputs[0].OutputValue" --output text` | jq -r '.DBInstances[] | .Endpoint.Address')
echo $DBUSER
echo $DBPASS
echo $DBHOST
echo $DBPORT
```

3. Depois de ter feito isso, conecte-se à sua instância do MySQL. Execute o seguinte comando para se conectar à sua instância MySQL:

```bash
mysql -h $DBHOST -P $DBPORT -u$DBUSER -p $DBPASS
```

4. Verifique a soma de verificação da tabela de funcionários na instância Graviton2 executando o comando abaixo no cliente mysql:

```bash
use employees; checksum table employees;
```

Você deve ver a saída semelhante a uma abaixo. Por favor, compare o valor da soma de verificação (checksum) com um do banco de dados original.

```
+---------------------+-----------+
| Table               | Checksum  |
+---------------------+-----------+
| employees.employees | 610052939 |
+---------------------+-----------+
1 row in set (0.24 sec)
```

5. Sair do cliente MySQL

### Neste ponto, você tem uma cópia totalmente funcional do seu banco de dados original usando o tipo de instace Amazon RDS e Graviton2 M6g.
