---
title: "Graviton2 e bases de dados"
chapter: true
weight: 60
---

O Graviton2 traz melhor desempenho x custo para seus bancos de dados do Amazon Relational Database Service (RDS), em comparação com a geração anterior de tipos de instâncias de banco de dados M5 e R5.


Nesta seção, exploraremos a execução do Amazon Relational Database Service (RDS) com instâncias de  Graviton2. 
Vamos nos concentrar no backend do banco de dados MySQL, no entanto, os processados descritos são aplicáveis a outros motores de banco de dados (MySQL 8.0.17 e superior, MariaDB 10.4.13 e superior, e PostgreSQL 12.3 e superior) suportados pelo RDS para Graviton2. 
Ao selecionar instância do tipo Graviton 2 para RDS e você pode escolher entre instâncias de familia M6g e R6g.

As instâncias M6g são ideais para cargas de trabalho de uso geral. As instâncias R6g oferecem 50% mais memória do que suas contrapartes M6g e são ideais para cargas de trabalho intensas de memória, como análise de Big Data.


Você pode explorar um dos dois caminhos para mover seu back-end de banco de dados de código aberto existente para instância do tipo Graviton2 : 

Opção 1) Criando um novo banco de dados a partir de snapshot de versão de banco de dados compatível (recomendado para processo de produção) 

Opção 2) Atualizando seu banco de dados mais antigo para uma versão suportada pelo RDS com Graviton2, e modificando o tipo de instância (ambiente de teste/dev)





