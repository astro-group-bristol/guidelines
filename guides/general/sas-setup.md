# XMM SAS Setup

Setting up the XMM SAS data analysis software

<!--BEGIN TOC-->

## Table of Contents

1. [Native installation](#native-installation)
   1. [M1 Mac](#m1-mac)
2. [Docker](#docker)
3. [Calibration files](#calibration-files)

<!--END TOC-->

## Native installation <a id="toc-tag-mdtoc" name="native-installation"></a>

General instructions about XMM-SAS can be found on the [XMM-Newton data analysis](https://www.cosmos.esa.int/web/xmm-newton/sas) page. This includes links to the [Download](https://www.cosmos.esa.int/web/xmm-newton/sas-download) and [Installation instructions](https://www.cosmos.esa.int/web/xmm-newton/sas-installation)

### M1 Mac <a id="toc-tag-mdtoc" name="m1-mac"></a>

This is what I (AJY) did to install SAS (as of 2023-01-19). There might be a more recent version of SAS you can download. Change the path to wherever you have downloaded the package (in this example `/Users/my_user/Software/xmm-sas/...`)

```
wget http://sasdev-xmm.esac.esa.int/pub/sas/20.0.0/macOS/11.6/sas_20.0.0-macOS-11.6.tgz
tar zxf sas_20.0.0-macOS-11.6.tgz
export SAS_PERL=/usr/bin/perl
./install.sh
source /Users/my_user/Software/xmm-sas/xmmsas_20211130_0941/setsas.sh
```

Note that I had to edit the `setsas.sh` file to change the line near the end to `ulimit -s 65520`. The larger value for the stack size that is in the file by default didn't work because it was above the hard limit. I don't really understand this but the workaround seems to work (anyone know more about this than me!?)

## Docker <a id="toc-tag-mdtoc" name="docker"></a>

You can also run SAS using Docker, although at present (as of 2023-01-20) this is x86 only (but it does run OK on Mac with Apple Silicon). See the [Docker for SAS](https://www.cosmos.esa.int/web/xmm-newton/sas-installation-docker4sas) page for instructions.

## Calibration files <a id="toc-tag-mdtoc" name="calibration-files"></a>

You will also need the most up-to-date XMM calibration files. I use `rsync` to keep these up to date (see instructions on [XMM-Newton Current Calibration Files](https://www.cosmos.esa.int/web/xmm-newton/current-calibration-files)). Change the path, `my_user/Software/...` in this case, to wherever you want the current calibration files to be stored.

```
rsync -v -a --delete --delete-after --force --include='*.CCF' --exclude='*/' sasdev-xmm.esac.esa.int::XMM_VALID_CCF /Users/my_user/Software/ccf/
```
