# HEASoft (which includes XSPEC) setup

<!--BEGIN TOC-->
## Table of Contents
1. [Native installation](#native-installation)
    1. [Native installation on M1 Mac](#native-installation-on-m1-mac)
2. [Docker installation](#docker-installation)
    1. [Starting a container](#starting-a-container)
    2. [Running with X11](#running-with-x11)
        1. [OSX](#osx)
        2. [Ubuntu / Debian](#ubuntu-/-debian)
3. [Calibration and CALDB](#calibration-and-caldb)
    1. [General calibration](#general-calibration)
    2. [NuSTAR calibration](#nustar-calibration)
4. [Running XSPEC on the group servers](#running-xspec-on-the-group-servers)
5. [Learning to use XSPEC](#learning-to-use-xspec)

<!--END TOC-->

## Native installation <a id="toc-tag-mdtoc" name="native-installation"></a>

The source code and native installation instructions are available on the [HEASoft website](https://heasarc.gsfc.nasa.gov/lheasoft/download.html).

### Native installation on M1 Mac <a id="toc-tag-mdtoc" name="native-installation-on-m1-mac"></a>

Here are notes about how I (AJY) installed HEASoft on my M1 Mac laptop (2023-01):

- Install `gcc@12` with homebrew (`brew install gcc@12`)
- Install Anaconda using the standard web installer, making sure the M1 Silicon version is used (because I unfortuantely couldn't get astropy to work with the Homebrew python)
- Install `astropy` with `conda install astropy`
- Set up the paths to compilers, etc. as suggested in the [instructions](https://heasarc.gsfc.nasa.gov/lheasoft/macos.html) (replacing `your_username` with your user name). I installed HEASoft in the folder where I unpacked heasoft (`your_path/heasoft-6.31.1` in the script below)

```
export CC=/usr/bin/clang
export CXX=/usr/bin/clang++
export PERL=/usr/bin/perl
export FC=/opt/homebrew/bin/gfortran-12
export PYTHON=/Users/your_username/opt/anaconda3/bin/python
cd /Users/your_username/your_path/heasoft-6.31.1/BUILD_DIR
./configure --prefix=/Users/your_username/your_path/heasoft-6.31.1 > config.txt 2>&1
make > build.log 2>&1
make install > install.log 2>&1
```

Then to run HEASoft use

```
export HEADAS=/Users/your_username/your_path/heasoft-6.31.1/aarch64-apple-darwin21.6.0
source $HEADAS/headas-init.sh
```

## Docker installation <a id="toc-tag-mdtoc" name="docker-installation"></a>

A pre-built Docker image is available from DockerHub [dustpancake/heasoft](https://hub.docker.com/r/dustpancake/heasoft). To obtain it, simply

```bash
docker pull dustpancake/heasoft
```

Note this image is a hefty 9.62GB in size, and is built directly from the [offical HEASoft Dockerfile](https://heasarc.gsfc.nasa.gov/lheasoft/docker.html) with the NICER mission-specific tools, and HEASoftPy.

Optionally, a version can be specified with `dustpancake/heasoft:version`. At the moment, the following versions are available:

- v6.30.1 (release: 11 April 2022)

### Starting a container <a id="toc-tag-mdtoc" name="starting-a-container"></a>

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

### Running with X11 <a id="toc-tag-mdtoc" name="running-with-x11"></a>

Running with [X11](https://x.org/wiki/) graphical support allows HEASoft to display windows on your screen, useful for any type of graphical manipulation or interactive plotting. Setting this up is very OS specific.

_If your Linux distributions or operating system is not listed, please open an issue and we'll add instructions._

#### OSX <a id="toc-tag-mdtoc" name="osx"></a>

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

#### Ubuntu / Debian <a id="toc-tag-mdtoc" name="ubuntu-/-debian"></a>

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

## Calibration and CALDB <a id="toc-tag-mdtoc" name="calibration-and-caldb"></a>

In order to run some of the mission-specific HEASoft tasks you will require calibration files for those missions (e.g., for _NuSTAR_ or _NICER_) as well as some mission non-specific general calibration information.

Instructions and more information on the [The HEASARC Calibration Database](https://heasarc.gsfc.nasa.gov/docs/heasarc/caldb/caldb_intro.html) web page.

For specific missions see the [Latest CALDB Files for Supported Missions](https://heasarc.gsfc.nasa.gov/docs/heasarc/caldb/caldb_supported_missions.html) web page.

### General calibration <a id="toc-tag-mdtoc" name="general-calibration"></a>

Follow the [How to Install a Calibration Database](https://heasarc.gsfc.nasa.gov/docs/heasarc/caldb/install.html) instructions.

Update `/Users/your_path/...` to the appropriate path. The following was up-to-date as of 2023-01-20.

```
export CALDB=/Users/your_path/caldb
cd $CALDB
wget https://heasarc.gsfc.nasa.gov/FTP/caldb/software/tools/caldb_setup_files.tar.Z
tar -zxvf caldb_setup_files.tar.Z
wget https://heasarc.gsfc.nasa.gov/FTP/caldb/data/gen/goodfiles_gen_20200625.tar.Z
tar -zxvf goodfiles_gen_20200625.tar.Z
```

Edit the path in `caldbinit.sh` then to set up CALDB use the following

```
export CALDB=/Users/your_path/caldb
source $CALDB/software/tools/caldbinit.sh
```

### NuSTAR calibration <a id="toc-tag-mdtoc" name="nustar-calibration"></a>

Up-to-date as of 2023-01-20. See [Latest CALDB Files for Supported Missions](https://heasarc.gsfc.nasa.gov/docs/heasarc/caldb/caldb_supported_missions.html). Note that at the moment a patch file is required for NuSTAR.

```
cd $CALDB
wget https://heasarc.gsfc.nasa.gov/FTP/caldb/data/nustar/fpm/goodfiles_nustar_fpm_20221229.tar.gz
tar -zxvf goodfiles_nustar_fpm_20221229.tar.gz
wget https://heasarc.gsfc.nasa.gov/FTP/caldb/data/nustar/fpm/goodfiles_nustar_fpm_clockcor_20221229.tar.gz
tar -zxvf goodfiles_nustar_fpm_clockcor_20221229.tar.gz
```

## Running XSPEC on the group servers <a id="toc-tag-mdtoc" name="running-xspec-on-the-group-servers"></a>

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

## Learning to use XSPEC <a id="toc-tag-mdtoc" name="learning-to-use-xspec"></a>

See the [XSPEC instruction manual](https://heasarc.gsfc.nasa.gov/docs/software/heasoft/xanadu/xspec/manual/XspecManual.html) which includes a [walk through tutorial](https://heasarc.gsfc.nasa.gov/docs/software/heasoft/xanadu/xspec/manual/node35.html).
