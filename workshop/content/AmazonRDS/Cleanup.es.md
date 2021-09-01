---
title: "Limpieza del laboratorio de RDS"
date: 2020-04-10T11:14:51-06:00
weight: 90
---

Para eliminar los recursos de RDS creados por este laboratorio por favor lanza el siguiente comando:

```bash
cd ~/environment/graviton2-workshop/
cdk destroy GravitonID-rds* -f
```

{{% notice warning %}}
En la consola RDS, asegúrese de eliminar las instancias creadas durante el laboratorio para evitar incurrir en costos adicionales.
{{% /notice %}}
