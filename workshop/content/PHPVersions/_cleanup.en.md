---
title: "Cleanup"
weight: 90
---


To cleanup resources created by this lab please issue following commands:

```bash
cd ~/environment/graviton2-workshop/
cdk destroy GravitonID-php-bench -f
```

{{% notice warning %}}
Visit EC2 console and ensure instances are terminated.
{{% /notice %}}

