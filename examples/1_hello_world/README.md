---
marp: true
---

# Example - MPI Hello World

For our first example we will build a docker container with MPI.

To build the container for this example, you will need to have docker or singularity installed locally within your environment.

---

## Locally (On Laptop Where docker is installed)

Navigate to the directory containing this example and run:

```bash
docker image build -t <username>/<repository_name>:<tag> --push .
```

Replacing username, repository_name, and tag with your own values.

---

### On TAMU Faster - Environment set-up

Make sure your environment is set-up:
