---
title: "Limpeza RDS"
date: 2020-04-10T11:14:51-06:00
weight: 90
---


Para limpar os recursos do RDS criados por este laboratório, execute o seguinte comando:

```
cd ~/environment/graviton2-workshop/
cdk destroy GravitonID-rds* -f
```

{{% notice warning %}}
Visite o console do RDS e certifique-se de excluir instantâneos criados durante o laboratório para evitar custos adicionais.
{{% /notice %}}

