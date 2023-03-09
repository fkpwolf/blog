---
layout: post
title: "Databend Inside"
date: 2023-1-1
categories:
  - Database
typora-root-url: ../../../blog
---
### Prepare workspace

### Inside

```
MySQL [(none)]> explain pipeline SELECT i FROM t1 where j > 12 limit 55;
+----------------------------------------------------------------------------------------------------------+
| explain                                                                                                  |
+----------------------------------------------------------------------------------------------------------+
| CompoundChunkOperator(Rename) × 1 processor                                                              |
|   CompoundChunkOperator(Project) × 1 processor                                                           |
|     LimitTransform × 1 processor                                                                         |
|       Merge (ExchangeSourceTransform × 20 processors) to (LimitTransform × 1)                            |
|         ExchangeSourceTransform × 20 processors                                                          |
|           CompoundChunkOperator(Project->Rename) × 20 processors                                         |
|             Merge (FuseEngineSource × 151 processors) to (CompoundChunkOperator(Project->Rename) × 20)   |
|               FuseEngineSource × 151 processors                                                          |
+----------------------------------------------------------------------------------------------------------+
8 rows in set (0.470 sec)
```


### Think
- The service is simple/startup. So it is easy to inspect the whole backend
- No legacy code. all new technology.
- Code naming is confused
- Tracing is good for multi-thread & distributed application
- In general, database is a CPU intensive and Memory consumption application