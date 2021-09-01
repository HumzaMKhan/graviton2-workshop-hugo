---
title: "Graviton2 y Bases de Datos"
chapter: true
weight: 60
---

Graviton2 brinda mejoras tanto en costo como en rendimiento en tus Bases de Datos Relacionales hospedadas en el servicio de Amazon RDS comparado con las generaciones anteriores de tipos de instancia M5 y R5.

En esta seccion exploraremos el servicio de Amazon Relational Database Service (RDS) con el nuevo procesador Graviton2.
Aunque nos enfocaremos en bases de datos MySQL, este proceso es aplicable para otras motores de bases de datos (MySQL 8.0.17+, MariaDB 10.4.13+, y tambien a PostgreSQL 12.3+ son soportados para el uso de RDS con Gravtion2.
Cuando seleccionemos tipos de instancia para RDS podras elegir entre la familia de instancias M6g y R6g.

- Las instancias M6g son ideas para cargas de trabajo de propósito general. 
- Las instancias R6g ofrecen 50% más memoria que las M6g y son ideales para cargas de trabajo que requieran uso de memoria intensiva, por ejemplo, analíticos y big data.

Podras explorar los dos caminos existentes para migrar tus bases de datos de código abierto a tipos de instancias con procesador Graviton2:

Opción 1) Crear una nueva base de datos con una versión compatible de motor de bases de datos a partir de un snapshot (recomendado para ambientes productivos).

Opción 2) Actualizar tu motor de bases de datos a una versión soportada por RDS con Graviton2, modificando el tipo de instancia de forma sencilla (ambientes de desarrollo o pruebas)
