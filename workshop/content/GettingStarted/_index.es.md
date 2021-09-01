---
title: "Introducción"
date: 2020-04-10T11:14:49-06:00
weight: 10
---

¡Bienvenido al workshop de Graviton2!

La intención de este workshop es educar a los usuarios sobre el uso de Graviton2 con los servicios y arquitecturas de AWS.
Aunque es recomendada, no se requiere experiencia en áreas específicas como contenedores, bases de datos, DevOps y/o Big Data. Este workshop está recomendado para arquitectos de nube, SREs, ingenieros de DevOps y otros profesionales de TI.

Este workshop debe ser ejecutado en alguna de las siguientes regiones de AWS: Este de Estados Unidos (N. Virginia, Ohio), Oeste de Estados Unidos (Oregon), y Europa (Ireland).

{{% notice info %}}
Si no estás en un evento de AWS, los servicios de AWS que utilices serán cargados a la cuenta de AWS en la que ejecutes estos laboratorios. Te recomendamos explorar la capa gratuita de AWS.
{{% /notice %}}

Los escenarios cubiertos en este workshop incluyen:

* Cluster de EKS con nodegroups multi-arquitectura (x86-64 and arm64).
* Cluster de ECS con un "service" y una "task" de ejemplo ejecutada en una instancia Graviton2.
* Un pipeline de integración contínua (CI) para imagenes de docker multi-arquitectura usando CodePipeline, CodeBuild, CodeCommit, y ECR (con manifests de docker).
* Un pipeline para ejecutar una aplicación hecha en .NET Core 5 en un clúster de Amazon EKS con nodos Graviton2.
* Un escenario de migración de MySQL 8 sobre una instancia tipo m5 a un MySQL en instancia m6g (Graviton2).
* Un escenario de migración de MySQL 5 sobre una instancia tipo m5 a una actualización a MySQL 8 y después un movimiento de instancia a m6g (Graviton2)
* Un clúster de EMR con un job de ejemplo hecho en Spark ejecutado sobre una instancia Graviton2

{{% notice info %}}
Ejecutar todos los escenarios tomará al rededor de 4 horas.
{{% /notice %}}

