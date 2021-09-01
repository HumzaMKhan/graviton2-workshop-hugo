---
title: "Criar instância MySQL do RDS"
date: 2020-04-10T11:14:51-06:00
weight: 10
---

Comece revisando o conteúdo da definição de tarefa RDS MySQL 5 no arquivo graviton2-workshop/graviton2/rds_graviton/rds_mysql_5.py

        db_mysql5 = rds.DatabaseInstance(self, "MySQL5",
                                             engine=rds.DatabaseInstanceEngine.mysql(
                                                 version=rds.MysqlEngineVersion.VER_5_7_31
                                             ),
                                             instance_type=ec2.InstanceType("m5.4xlarge"),
                                             vpc=vpc,
                                             multi_az=False,
                                             publicly_accessible=True,
                                             allocated_storage=100,
                                             storage_type=rds.StorageType.GP2,
                                             cloudwatch_logs_exports=["audit", "error", "general", "slowquery"],
                                             deletion_protection=False,
                                             enable_performance_insights=True,
                                             delete_automated_backups=True,
                                             backup_retention=core.Duration.days(1),
                                             parameter_group=rds.ParameterGroup.from_parameter_group_name(
                                                 self, "para-group-mysql",
                                                 parameter_group_name="default.mysql5.7"
                                             )
                                             )

O uso do AWS Cloud Development Kit (CDK) simplifica muito a instanciação de serviços da AWS, como o Amazon RDS. 
O fragmento de código Python acima permite que você instancie um Single-AZ RDS MySQL 5 com volume GP2 Amazon EBS de 100 GB. Esta instância está usando o tipo de instância m5.4xlarge e só será acessível a partir do seu ambiente Cloud9 via IP público. 
A instância do RDS criada através do CDK também faz que a instancia tenha políticas de backup simples, registro em log e Performance Insights de RDS habilitados.

1. Iniciar a implantação do RDS MySQL 5 usando : 

```bash
cdk deploy GravitonID-rds-5
```

2. Antes de nos conectarmos à instância do MySQL, precisamos definir o nome de usuário e senha como variáveis de ambiente em nossa sessão do Cloud9. Usaremos a saída do CloudFormation para encontrar variáveis relevantes.

```bash
CREDS=$(aws secretsmanager get-secret-value --secret-id `aws cloudformation describe-stacks --stack-name GravitonID-rds-5 --query "Stacks[0].Outputs[0].OutputValue" --output text` | jq -r '.SecretString')
export DBID=$(aws cloudformation describe-stacks --stack-name GravitonID-rds-5 --query "Stacks[0].Outputs[1].OutputValue" --output text)
export DBUSER="`echo $CREDS | jq -r '.username'`"
export DBPASS="`echo $CREDS | jq -r '.password'`"
export DBHOST="`echo $CREDS | jq -r '.host'`"
export DBPORT="`echo $CREDS | jq -r '.port'`"
echo $DBUSER
echo $DBPASS
echo $DBHOST
echo $DBPORT
echo $DBID
```

3. Se você vir uma saída de seu nome de usuário, senha, servidor e porta do banco de dados, você definiu corretamente as variáveis de ambiente. 

Prossiga para o próximo capítulo “Importação banco de dados”.
