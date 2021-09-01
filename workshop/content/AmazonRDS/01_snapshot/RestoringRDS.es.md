---
title: "Restauración del snapshot en una instancia con Graviton2"
date: 2020-04-10T11:14:51-06:00
weight: 30
---


Ya estamos listos para hacer la recuperación de la base de datos en una nueva instancia con Graviton2.

Revisa los  contenidos del archivo de definicion de la instancia de bases de datos RDS MySQL 8 que se encuentra en la ruta graviton2-workshop/graviton2/rds_graviton/ds_restore.py

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

{{% notice info %}}
Para minimizar los costos de ejecución de este lab, vamos a implementar la instancia RDS en una única zona de disponibilidad.
{{% /notice %}}

1. Usaremos el snapshot que creamos de la instancia original con arquitectura x86-64 para hacer la restauración en la nueva instancia de Graviton2. Ejecuta el código siguiente para crear la instancia de Graviton2:

```bash
cdk deploy GravitonID-rds-restore
```

2. Antes de conectarte a la instancia de Graviton2 con MySQL actualizaremos el nombre de usuario y contraseña en las variables de ambiente de la sesión de Cloud9.

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

3. Es momento de conectarnos a la instancia de MySQL. Ejecuta el siguiente código en tu instancia:

```bash
mysql -h $DBHOST -P $DBPORT -u$DBUSER -p$DBPASS
```

4. Verifica que la tabla de empleados en la instancia con Graviton2 sea correcta ejecutando el checksum:

```bash
use employees; checksum table employees;
```

El resultado será una tabla similar a la de abajo. Compara estos resultados con el checksum original que escribiste en el capitulo anterior

```
+---------------------+-----------+
| Table               | Checksum  |
+---------------------+-----------+
| employees.employees | 610052939 |
+---------------------+-----------+
1 row in set (0.24 sec)
```

5. Sal del cliente de MySQL

### En este momento ya cuentas con una copia funcional de tu base de datos original en Amazon RDS con Graviton2 usando un tipo de instancia M6g
