# JADE

<!--BEGIN TOC-->

## Table of Contents

1. [Getting access](#getting-access)
2. [Partitions](#partitions)

<!--END TOC-->

- [Documentation](https://docs.jade.ac.uk/en/latest/index.html)

Resources:

- 63 DGX-MAX-Q Nodes, each with 8 Nvidia V100 GPUs
- 4 Login nodes
- Mellanox EDR networking
- 4TB of local SSD storage per node
- Over 1PB of DDN AI400X storage (70TB of NVMe)

## Getting access <a id="toc-tag-mdtoc" name="getting-access"></a>

- [Getting an account](https://docs.jade.ac.uk/en/latest/jade/getting-account.html)

## Partitions <a id="toc-tag-mdtoc" name="partitions"></a>

The partitions have the following limits for submitted jobs:

| Partition name | Partition Size | Job Walltime limit | Running Job limit | Description                                                         |
| -------------- | -------------- | ------------------ | ----------------- | ------------------------------------------------------------------- |
| big            | 30 nodes       | 24 hours           | 5 Jobs            | Partition dedicated to jobs that occupy an entire node, i.e. 8 GPUs |
| small          | 30 nodes       | 6 days             | 8 Jobs            | Partition dedicated to jobs that utilise a single GPUs each.        |
| devel          | 3 nodes        | 1 hour             | 1 Job             | Partition dedicated to testing.                                     |
