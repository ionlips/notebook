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

````ai
You started it with `--rm`, so pressing Ctrl-C stops the container and deletes it. To bring it back, run the same command again:

```bash
cd ~/dev/ichec/caspir-digital-twin
docker run -it --rm --name slurmsim -h slurmsim \
    -p 127.0.0.1:8787:8787 \
    -v ~/dev/ichec/caspir-digital-twin/sim-work:/home/slurm/work \
    nsimakov/slurm_sim:v3.0
```

- **Kept:** everything in `/home/slurm/work`, because it's really your `sim-work/` folder.
- **Lost:** everything else inside the container, including the MariaDB database the tutorial filled in, any packages you installed and RStudio settings. Each time you'll need to re-run the notebook's setup steps (creating the accounts and loading users).

## To keep the container between sessions

Create it once without `--rm` and run it in the background with `-d`:

```bash
docker run -d --name slurmsim -h slurmsim \
    -p 127.0.0.1:8787:8787 \
    -v ~/dev/ichec/caspir-digital-twin/sim-work:/home/slurm/work \
    nsimakov/slurm_sim:v3.0
```

From then on:

```bash
docker stop slurmsim     # shut it down; nothing is lost
docker start slurmsim    # bring it back exactly as it was
docker logs slurmsim     # see its startup output
docker ps -a             # check whether it's running
```

To start completely fresh, run `docker rm -f slurmsim` and create it again.

If a container named `slurmsim` still exists, Docker will refuse to create a new one with that name. In that case either `docker start slurmsim` or remove it first with `docker rm -f slurmsim`.
````
