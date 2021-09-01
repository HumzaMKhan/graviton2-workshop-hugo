---
title: "Começando"
date: 2020-04-10T11:14:49-06:00
weight: 10
---

Este primeiro passo é focado na implantação de todos os pré-requisitos para o Graviton2 Labs. Para fins deste laboratório, usaremos os seguintes serviços e componentes de software da AWS:
* [Cloud9 IDE](https://aws.amazon.com/cloud9/)
* [AWS CDK](https://docs.aws.amazon.com/cdk/latest/guide/home.html) (Cloud Development Kit)
  
Usaremos o [AWS CDK] (https://docs.aws.amazon.com/cdk/latest/guide/home.html) para implantar alguns pré-requisitos. Nosso princípio orientador é que usaremos o CDK para implantar infraestrutura estática e pré-requisitos.


### Configurar um IDE Cloud9

1. No console da AWS, acesse o serviço Cloud9 e selecione “Criar ambiente”. Chame seu novo IDE de `graviton2ide` e clique em 'Próximo passo'. 

Na próxima tela, selecione “Outro tipo de instância” e escolha `m5.xlarge` tipo de instância. Clique em “Próxima etapa” novamente.

Na página final, clique em “Criar ambiente”. Certifique-se de deixar as configurações da VPC nos valores padrão.

Depois que o ambiente for criado, você será redirecionado automaticamente para o IDE. Leve um minuto para explorar a interface e observe que você pode alterar o esquema de cores se desejar (menu do AWS Cloud9 -> Preferências -> Temas). 

Em seguida, vamos atualizar o ambiente Cloud9 para permitir que você execute os laboratórios a partir do ambiente.

* Crie uma função para o ambiente Cloud9 clicando no seguinte [Link](https://console.aws.amazon.com/iam/home#/roles$new?step=review&commonUseCase=EC2%2BEC2&selectedUseCase=EC2&policies=arn:aws:iam::aws:policy%2FAdministratorAccess) 
* Confirme se o serviço da AWS e o EC2 estão selecionados e clique em ao lado de "exibir permissões". 
* Confirme se o AdministratorAccess está marcado e clique em Próximo: Tags para atribuir tags. 
* Deixe os padrões e clique em Próximo: Revisar para revisar. 
* Digite `Cloud9-Admin-Role` para o campo Nome e clique em Criar função. 
* Quando este novo perfil for criado, vá para o EC2 e encontre a instância do Cloud9, e atribua o perfil da instância a esta instância. 
* Acesse Preferências do Cloud9 e, em Credenciais da AWS, desative as credenciais temporárias gerenciadas pela AWS”.


2. Passar para a etapa “Pré-requisitos”



