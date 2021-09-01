---
title: "Espaço de trabalho"
date: 2020-04-10T11:14:49-06:00
weight: 20
---

Este primeiro passo é focado em todos os pré-requisitos para os laboratórios de Graviton2. Para fins deste laboratório, usaremos os seguintes serviços e componentes de software da AWS:
* [Cloud9 IDE](https://aws.amazon.com/cloud9/)
* [AWS CDK](https://docs.aws.amazon.com/cdk/latest/guide/home.html) (Cloud Development Kit)
  
Nós usaremos o [AWS CDK](https://docs.aws.amazon.com/cdk/latest/guide/home.html) para implantar alguns pré-requisitos. 
Nosso objetivo é que usaremos o CDK para implantar infraestrutura estática e os pré-requisitos.

{{% notice warning %}}
O espaço de trabalho do Cloud9 deve ser criado por um usuário do IAM com privilégios de administrador, não pelo usuário da conta raiz. Certifique-se de que você está conectado como um usuário do IAM, não o usuário da conta raiz.
{{% /notice %}}

{{% notice info %}}
Uma lista de navegadores compatíveis para o AWS Cloud9 pode ser encontrada [here]( https://docs.aws.amazon.com/cloud9/latest/user-guide/browsers.html).
{{% /notice %}}

{{% notice tip %}}
Os bloqueadores de anúncios, desabilitadores javascript e bloqueadores de rastreamento devem ser desativados para o domínio cloud9 ou a conexão ao espaço de trabalho pode ser afetada. O Cloud9 requer cookies de terceiros. Você pode colocar a lista de permissões para [domínios específicos]( https://docs.aws.amazon.com/cloud9/latest/user-guide/troubleshooting.html#troubleshooting-env-loading).
{{% /notice %}}

### Set up a Cloud9 IDE

1. No console da AWS, acesse o serviço Cloud9 e selecione `Create environment`.  Chame o seu novo IDE `Graviton2IDE` e clique `Next Step`.  
Na próxima tela, selecione "Other instance type" e escolha `m5.xlarge` como o tipo de instancia. Clique em `Next step` novamente.  
No final da pagina, clique `Create environment`.  Certifique-se de deixar as configurações da VPC nos valores padrão.

Depois que o ambiente for criado, você redirecionará automaticamente para o IDE. Tome um minuto para explorar a interface, e note que você pode alterar o esquema de cores se quiser (AWS Cloud9 menu -> Preferences -> Themes).

Em seguida, vamos atualizar o ambiente Cloud9 para permitir que você execute os laboratórios a partir do ambiente.

* Crie uma função para o seu ambiente Cloud9 clicando no seguinte [link](https://console.aws.amazon.com/iam/home#/roles$new?step=review&commonUseCase=EC2%2BEC2&selectedUseCase=EC2&policies=arn:aws:iam::aws:policy%2FAdministratorAccess)
* Confirme se o serviço da AWS e o EC2 estão selecionados e clique em Next para ver as permissões.
* Confirme que AdministratorAccess está selecionado e, em seguida, clique em Next: Tags para atribuir etiquetas.
* Deixe os padrões e clique em Next: Review to review.
* Digite `Cloud9-Admin-Role` no campo Name e clique Create role. 
* Depois que esse novo perfil for criado, acesse o EC2 e encontre a instância do Cloud9 e atribua o perfil da instância a essa instância.
* Vá para Preferências do Cloud9 e em AWS Credentials desabilite `AWS managed temporary credentials`.  


2. Vá ao seguinte passo: "Pré-requisitos".



