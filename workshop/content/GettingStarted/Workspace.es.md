---
title: "Workspace"
date: 2020-04-10T11:14:49-06:00
weight: 20
---

Este primer paso se encofa en la implementación de todos los prerrequisitos para ejecutar los laboratorios de Graviton2. Utilizaremos los siguientes componentes:
* [Cloud9 IDE](https://aws.amazon.com/cloud9/)
* [AWS CDK](https://docs.aws.amazon.com/cdk/latest/guide/home.html) (Cloud Development Kit)
  
Utilizaremos el AWS CDK para implementar algunos de los prerrequisitos e infraestructura base (https://docs.aws.amazon.com/cdk/latest/guide/home.html) 

{{% notice warning %}}
El workspace de Cloud9 debe haber sido creado por un usuario con privilegios de administrador,
no la cuenta raíz (root). Asegúrate de haber iniciado sesión como un usuario IAM y no con la cuenta raíz.
{{% /notice %}}

{{% notice info %}}
Una lista de los navegadores soportados para utilizar AWS Cloud9 puede ser encontrada [aqui]( https://docs.aws.amazon.com/cloud9/latest/user-guide/browsers.html).
{{% /notice %}}

{{% notice tip %}}
Inhibidores de anuncios, de código javascript y de seguimiento deben ser deshabilirtados para el dominio de cloud9, de lo contrario el funcionamiento podría no ser el óptimo.
Cloud9 requiere cookies de terceros. Deberás agregar a tu lista blanca estos [dominios especificos]( https://docs.aws.amazon.com/cloud9/latest/user-guide/troubleshooting.html#troubleshooting-env-loading).
{{% /notice %}}

### Crea un IDE de Cloud9

1. En la consola web de AWS ve al servicio de Cloud9 y selecciona `Create environment`. Nombra tu IDE `Graviton2IDE` y da clic en `Next Step`.
En la siguiente pantalla, selecciona "Other instance type" y selecciona `m5.xlarge`. Da clic nuevamente en `Next step`.  
En la página final, da clic en `Create environment`. Asegúrate de haber dejado la configuración de la VPC con los valores por defecto.

Una vez el ambiente haya terminado de ser construido, se te redireccionará automáticamente hacia el IDE. Tómate un momento para explorar la interfase y si así lo deseas, puedes cambiar el esquema de colores al que más te guste dando clic en el menú de AWS Cloud9 - Preferences - Themes.

Ahora vamos a actualizar el ambiente de cloud9 para que puedas ejecutar los laboratorios en él.

* Crea un rol para tu ambiente de cloud9 dando clic en la siguiente [liga](https://console.aws.amazon.com/iam/home#/roles$new?step=review&commonUseCase=EC2%2BEC2&selectedUseCase=EC2&policies=arn:aws:iam::aws:policy%2FAdministratorAccess)
* Confirma que el servicio de AWS EC2 haya sido seleccionado y después da clic en Siguiente para revisar los permisos.
* Confirma que "AdministratorAccess" esté seleccionado y da clic en Siguiente.
* Deja todo lo demás con los valores por defecto y da clic en Review
* Nombra el rol como `Cloud9-Admin-Role` y da clic en Create role. 

* Una vez este perfil haya sido creado, ve a EC2 y encuentra la instancia de Cloud9. Asígnale ese rol a esta instancia.
* Ve a Preferencias de Cloud9 y bajo el menú AWS Credentials, deshabilita `AWS managed temporary credentials`.  


2. Ahora vamos al paso de prerrequisitos



