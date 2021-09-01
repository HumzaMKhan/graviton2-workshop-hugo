---
title: "Limpeza EMR"
weight: 40
---

Para eliminar o cluster EMR execute o seguinte comando: 

```bash
aws emr terminate-clusters --cluster-ids $EMR_CLUSTER_ID
```

Depois de analisar o conteúdo do bucket do S3, você pode optar por esvaziar e excluir saídas de trabalho por meio do console do AWS S3 ou usando a CLI.
