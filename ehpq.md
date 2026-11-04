---
date: 2026-11-03
keywords: []
---
# CASPIr digital twin

```shell
docker pull nsimakov/slurm_sim:v3.0
mkdir -p ~/dev/ichec/caspir-digital-twin/sim-work
docker run -it --rm --name slurmsim -h slurmsim \
    -p 127.0.0.1:8787:8787 \
    -v ~/dev/ichec/caspir-digital-twin/sim-work:/home/slurm/work \
    nsimakov/slurm_sim:v3.0
```
