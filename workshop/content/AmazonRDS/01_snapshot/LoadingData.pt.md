---
title: Importação de banco de dados de amostra
date: 2020-04-10T11:14:51-06:00
weight: 20
---

1. Antes de começarmos este laboratório, certifique-se de que você está trabalhando em sua pasta graviton2-workshop. Se você não estiver, por favor mude para a pasta executando o seguinte comando em seu shell bash:

```bash
cd ~/environment/graviton2-workshop/
```

Vamos começar clonando um banco de dados de exemplo para MySQL.

```bash 
git clone https://github.com/datacharmer/test_db.git
cd test_db
```

2. Depois de ter feito isso, conecte-se à sua instância do MySQL. Execute o seguinte comando para se conectar à sua instância MySQL:

```bash
mysql -h $DBHOST -P $DBPORT -u$DBUSER -p$DBPASS
```

Se você estiver enfrentando problemas de login devido à falta de variáveis de ambiente, use os seguintes comandos para definir as credenciais.

```bash
CREDS=$(aws secretsmanager get-secret-value --secret-id `aws cloudformation describe-stacks --stack-name GravitonID-rds-8 --query "Stacks[0].Outputs[0].OutputValue" --output text` | jq -r '.SecretString')
export DBUSER="`echo $CREDS | jq -r '.username'`"
export DBPASS="`echo $CREDS | jq -r '.password'`"
export DBHOST="`echo $CREDS | jq -r '.host'`"
export DBPORT="`echo $CREDS | jq -r '.port'`"
echo $DBUSER
echo $DBPASS
echo $DBHOST
echo $DBPORT
```



3. Se você estiver conectado à sua instância do MySQL, você pode carregar um banco de dados de demonstração para sua instância do RDS. Para carregar os dados no banco de dados, execute o seguinte comando a partir do cliente MySQL.

```bash
source employees.sql
```

O motor deve começar a carregar os dados por conta própria e você deve ver saídas semelhantes a :
```
Query OK, 7671 rows affected (0.05 sec)
Records: 7671  Duplicates: 0  Warnings: 0

+---------------------+
| data_load_time_diff |
+---------------------+
| 00:00:33            |
+---------------------+
1 row in set (0.00 sec)

mysql> 
```

4. Verificar a soma de verificação(checksum) da tabela de funcionários executando :

```bash
use employees; checksum table employees;
```

Você deve ver a saída semelhante à abaixo. Por favor, anote o valor da soma de verificação da soma de verificação original da tabela do banco de dados.

```
+---------------------+-----------+
| Table               | Checksum  |
+---------------------+-----------+
| employees.employees | 610052939 |
+---------------------+-----------+
1 row in set (0.24 sec)
```

5. Saia do cliente MySQL e crie um snapshot do banco de dados executando o seguinte comando:

```bash
cd ~/environment/graviton2-workshop/
 ~/environment/graviton2-workshop/scripts/rds-snapshot.sh 
```
The script will return a randomly generated snapshot id.

6. Monitore o progresso do snapshot usando o console da AWS ou executando o seguinte comando:

```bash 
aws rds describe-db-snapshots --db-snapshot-identifier `aws ssm get-parameter --name "graviton_rds_lab_snapshot" | jq -r .Parameter.Value` | jq -r .DBSnapshots[0].Status
```
7. Quando o status do snapshot mude para “disponível”, você pode prosseguir para o próximo capítulo.


