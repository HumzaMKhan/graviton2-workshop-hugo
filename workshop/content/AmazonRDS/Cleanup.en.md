---
title: "RDS Cleanup"
date: 2020-04-10T11:14:51-06:00
weight: 90
---


To cleanup RDS resources created by this lab please issue following command :

```bash
cd ~/environment/graviton2-workshop/
cdk destroy GravitonID-rds* -f
```

{{% notice warning %}}
Visit RDS console and ensure you delete snapshots created during the lab to avoid incurring additional cost.
{{% /notice %}}

