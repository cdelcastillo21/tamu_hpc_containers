---
marp: true
---

# TAMU HPC Containers

The following repository contains the code, examples, and documentation for running HPC compliant containers on Texas A&M's HPC Clusters.
This repository originated from the workshop "Containers for HPC Reproducibility: Building, Deploying, and Optimizing" presented at the SEVENTH ANNUAL TEXAS A&M RESEARCH COMPUTING SYMPOSIUM on Tuesday May 21, 2024 - 

---


## Seventh Annual Texas A&M Research Computing Symposium - "Containers for HPC Reproducibility: Building, Deploying, and Optimizing"

To build the containers in this example you'll need docker installed locally:

- Docker Engine - https://docs.docker.com/engine/install/, but preferably Docker Desktop:
    - Mac - https://docs.docker.com/desktop/install/mac-install/
    - Windows - https://docs.docker.com/desktop/install/windows-install/
- Singularity  
    -

> **Note** - You'll probably need a good 20-40 GB of disk space free to comfortably build containers locally without having to clear your cache repeatedly.

Furthermore you'll need to create a profile on Docker Hub, and create an image repository for it - https://hub.docker.com

## Setting up Tutorial Environment

On Faster, you're going to want to create a folder on scratch to store everything

```bash
cd $SCRATCH
mkdir container_workshop
cd container_workshop
pwd
export TRAINING=/scratch/training/singularity
ls $TRAINING
```
ls $TRAINING

## Singularity Tutorial

Here's a markdown tutorial with slides separated by '---', including the provided content with appropriate bash code blocks:

---

## Singularity Image Formats

Singularity container images come in two main formats:
1. Directory
2. Single file. Singularity uses the SIF format for single file images. This is the default.

The singularity build tool can convert images in both formats.

```bash
singularity build --help
```

The `--sandbox` option is used to create directory-format images.

---

## Singularity Image Exercise

`singularity pull` can fetch an image and write to either file format (note the order of the arguments).

```bash
singularity pull almalinux.sif docker://almalinux:8
```

Singularity can convert an image to the directory file format. Use the `--sandbox` argument to specify the directory type (note the order of the arguments).

```bash
singularity build --sandbox $TMPDIR/almalinux almalinux.sif
```

---

## Singularity Write Exercise

Directory images are writable. Simply add the `--writable` flag to your container command.

```bash
singularity shell --writable $TMPDIR/almalinux
mkdir /my_dir
exit
```

Are the changes still there?

```bash
singularity shell $TMPDIR/almalinux
ls /
```

---

## Singularity Read-only Exercise

SIF files are safe for network file system /scratch.

```bash
singularity build --fakeroot my_almalinux.sif $TMPDIR/almalinux
```

Are the changes still there?

```bash
singularity shell my_almalinux.sif
ls /
exit
```

What about the `--writable` flag?

```bash
singularity shell --writable my_almalinux.sif
```

No.

---

## Working with Containers

---

## Launching Processes

Singularity has three methods for launching processes:
- Interactive: `singularity shell`
- Batch processing: `singularity exec`
- Container-as-executable: `singularity run`

---

## Singularity Run Exercise

`singularity run` will execute the default runscript, if one was defined. You may also execute the container directly.

```bash
singularity pull docker://hello-world
singularity run hello-world_latest.sif
./hello-world_latest.sif
```

Docker hello-world is a minimal image. This is all it can do.

---

## Singularity Exec Exercise

`singularity exec` lets you access executables and other commands in a container. This is appropriate for batch jobs.

ACES nodes have Python 3.

```bash
python3 --version
```

Our singularity image has a different Python 3.

```bash
singularity exec scipy-notebook_latest.sif python3 --version
```

---

## Working with Files

- Filesystem inside a container is isolated from the real, physical filesystem.
- To access your files, ensure the directory is mounted.
- By default, Singularity will mount `$HOME` and `$PWD` if it can.
- To specify additional directories, use the `SINGULARITY_BINDPATH` environment variable or the `--bind` command line option.

---

## Working with Files Exercise

Recommended that you mount `/scratch` to get access to your data storage, and `/tmp` to get access to the local disk on the node.

```bash
singularity shell --bind "/scratch,/tmp" <image>
mkdir $TMPDIR/my_dir; exit
ls $TMPDIR
```

Notice that your variables like `$TMPDIR` get passed into the container by default.

---

## Docker vs. Singularity Commands

| **Action**                | **Docker Command**                          | **Popular Docker Flags**                | **Singularity Command**                  | **Popular Singularity Flags**            |
|---------------------------|---------------------------------------------|-----------------------------------------|------------------------------------------|------------------------------------------|
| **Build Image**           | `docker build -t <image_name> .`            | `-t` (tag), `-f` (Dockerfile)           | `singularity build <image.sif> <recipe>` | `--sandbox`, `--writable`                |
| **Tag Image**             | `docker tag <image_id> <repository:tag>`    | N/A                                     | N/A                                      | N/A                                      |
| **Push Image**            | `docker push <repository:tag>`              | N/A                                     | `singularity push <image.sif> <library>` | N/A                                      |
| **Run Container**         | `docker run <image_name>`                   | `-d` (detached), `-p` (port), `-v` (volume) | `singularity run <image.sif>`            | `--bind`, `--contain`                    |
| **Execute Command**       | `docker exec -it <container_id> <command>`  | `-it` (interactive terminal)            | `singularity exec <image.sif> <command>` | `--bind`, `--contain`                    |
| **List Images**           | `docker images`                             | N/A                                     | `singularity images`                     | N/A                                      |
| **List Containers**       | `docker ps`                                 | `-a` (all containers)                   | `singularity instance list`              | N/A                                      |
| **Remove Image**          | `docker rmi <image_id>`                     | `-f` (force)                            | `singularity image rm <image.sif>`       | N/A                                      |
| **Remove Container**      | `docker rm <container_id>`                  | `-f` (force)                            | `singularity instance stop <instance>`   | N/A                                      |
| **Inspect Container**     | `docker inspect <container_id>`             | N/A                                     | `singularity inspect <image.sif>`        | N/A                                      |
| **View Logs**             | `docker logs <container_id>`                | `-f` (follow)                           | `singularity logs <instance>`            | N/A                                      |

---

### Notes:
- **Docker**: Primarily used for building, tagging, pushing, running, and managing containers.
- **Singularity**: Often used in HPC environments, supports running Docker images directly, and focuses on reproducibility and security.
