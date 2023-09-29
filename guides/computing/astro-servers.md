# Astro group servers

## Getting access (for members of the department only)

Ask Rhys Morris.

Servers available:

- `aquila` (Login Server – can log in to this without UoB VPN and access other servers from there)
- `typhon` (CDT priority, has a GPU)
- `trappist` (Zoe's group priority)
- `mensa`
- `cetus` (Ubuntu, cannot be accessed though Aquila, you must be on university network or VPN)

Load look-up (not working): [Load look-up](https://www.star.bris.ac.uk/local/notes/servers.html)

For the local page with more information about software and others see: [star.bris.ac.uk](https://www.star.bris.ac.uk/local/)

How to login:

```bash
ssh username@<server-name>.star.bris.ac.uk
```

You will then be promoted to enter your password.

## Be NICE

See [here about `ionice`](https://data-flair.training/blogs/ionice-command-in-linux/)

## Typhon

### GPU

Typhon has a GPU! NVIDIA TU104GL (Tesla T4) 2560 cores, 16gb RAM Turing cores! This is a AI specific GPU and so is best suited for training an ML model.

You can get an overview of the usage using NVIDIA tools:

```
lx21966@hm00: ~ $ nvidia-smi
Fri Sep 29 11:09:25 2023
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 450.80.02    Driver Version: 450.80.02    CUDA Version: 11.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Tesla T4            Off  | 00000000:03:00.0 Off |                    0 |
| N/A   44C    P0    27W /  70W |  14630MiB / 15109MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A    125780      C   ...conda3/envs/tf/bin/python    14627MiB |
+-----------------------------------------------------------------------------+
```

The CUDA compiler is available at

```
/usr/local/cuda/bin/nvcc
```

Most of the time, you'll want to use some framework for GPU programming, such as [OpenACC](https://www.openacc.org/).

Julia does it best.

C++20 and onwards (@RhysM, do we have compatible compilers?) can leverage the execution policy system to trivially offload STL algorithms onto GPU devices.

For more info, ask Rhys Morris.
