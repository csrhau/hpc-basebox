# hpc-basebox
Docker images of Linux distros with HPC compilers and libraries pre-installed


## Running Instructions

```bash
docker run -it --cap-add=SYS_PTRACE --security-opt seccomp=unconfined ubuntu-combo:latest 
```
