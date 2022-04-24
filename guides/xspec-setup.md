# XSPEC Setup

## Native installation

The source code and native installation instructions are available on the [HEASoft website](https://heasarc.gsfc.nasa.gov/lheasoft/download.html).

## Docker

A pre-built Docker image is available from DockerHub [dustpancake/heasoft](https://hub.docker.com/r/dustpancake/heasoft). To obtain it, simply
```bash
docker pull dustpancake/heasoft
```

Note this image is a hefty 9.62GB in size, and is built directly from the [offical HEASoft Dockerfile](https://heasarc.gsfc.nasa.gov/lheasoft/docker.html) with the NICER mission-specific tools, and HEASoftPy.

Optionally, a version can be specified with `dustpancake/heasoft:version`. At the moment, the following versions are available:

- v6.30.1 (release: 11 April 2022)

### Starting a container

To start HEASoft with persistent storage, navigate to your data directory and run:
```
docker run \
    --name heasoft \
    --rm \
    -it \
    -v $(pwd):/data \
    dustpancake/heasoft:6.30.1 tcsh
```

Note that only data stored in the container's `/data` directory will be saved on your local machine's disk.

### Running with X11

Running with [X11](https://x.org/wiki/) graphical support allows HEASoft to display windows on your screen, useful for any type of graphical manipulation or interactive plotting. Setting this up is very OS specific.

*If your Linux distributions or operating system is not listed, please open an issue and we'll add instructions.*

#### OSX
For OSX, it is recommended you install [XQuartz through Homebrew](https://formulae.brew.sh/cask/xquartz), which will install the full `libx11` and X11 server utilities.

Next, start XQuartz from the application menu.

We may then permit localhost connections to the X window manager by adding `127.0.0.1` as a known host:
```bash
/opt/X11/bin/xhost + 127.0.0.1
```

We can then spin up a container, adding a `DISPLAY` environment variable:
```
docker run \
    --name heasoft \
    --rm \
    -it \
    -e DISPLAY=host.docker.internal:0 \
    -v /path/to/data/dir:/data \
    dustpancake/heasoft:6.30.1 tcsh
```

#### Ubuntu / Debian
Debian (and therefore Ubuntu) is transitioning from X11 to Wayland. If this is the case for the version you are running, you may still have to install X11
```bash
sudo apt-get install xorg x11-xserver-utils
```
A reboot may then be required.

If you already have X11 installed, you can mount the authorities file in the container and pass the `DISPLAY` environment variable:
```
docker run \
    --name heasoft \
    --rm \
    -it \
    --v $HOME/.Xauthority:/home/heasoft/.Xauthority \
    -e DISPLAY \
    -v /path/to/data/dir:/data \
    dustpancake/heasoft:6.30.1 tcsh
```

## Running XSPEC on the group servers

HEASoft 6.29 is installed on the astrophysics group server.

Either run the following or add it to your `.bashrc` file:
```
export HEADAS=/usr/local/heasoft-6.29/x86_64-pc-linux-gnu-libc2.17
alias heainit="source $HEADAS/headas-init.sh"
```

The HEASoft software may then be access with
```
heainit
```

To build additional models, enable the version 9 developer toolset in your shell:
```bash
scl enable devtoolset-9 bash
```