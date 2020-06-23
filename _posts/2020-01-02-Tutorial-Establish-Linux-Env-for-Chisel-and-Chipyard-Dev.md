---
layout: post
title: "[Tutorial] Establish Linux Environment for Chisel and Chipyard Developments"
description: "This tutorial will help you to establish a Linux environment for Chisel and Chipyard development quickly with little error."
categories: [Tutorial]
tags: [Chipyard, RISC-V, Scala, Sbt, Mill]
last_updated: 2020-06-23 23:02:00 GMT+8
excerpt: This tutorial will help you to establish a Linux environment for Chisel and Chipyard development quickly with little error.
redirect_from:
  - /2020/01/03/
---

* Kramdown table of contents
{:toc .toc}
# Why need a tutorial for Establishing Chisel Environment?

I develop the projects in Virtual Ubuntu Linux, and at the beginning, I always met kinds of errors when I establish the development environment for Chisel which is time-costing and annoying, and I destroyed at least three Virtual Machines because of the wrong environments. Then I concluded several right steps to do that. I tried the steps when I gave up VM and created a new Ubuntu with Virtual Box, it worked well without any error.

# Pre- Set Up

## 1. Install Some Dependences

```shell
cd ~
sudo apt-get install vim git autoconf automake autotools-dev curl device-tree-compiler libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev iverilog gtkwave verilator yosys scala python-pip

sudo pip install jupyter notebook
```

## 2. Sbt

The build tool for Scala.

```shell
echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
curl -sL "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x2EE0EA64E40A89B84B2DF73499E82A75642AC823" | sudo apt-key add
sudo apt-get update
sudo apt-get install sbt
```

## 2. Mill (optional)

[Mill](https://github.com/lihaoyi/mill) is another Scala build tool, i.e., an alternative choice of Sbt.

------

Added on 23 June, 2020.

You can find the newest release number on [this document](http://www.lihaoyi.com/mill/#manual).

------

```shell
sudo su
curl -L https://github.com/lihaoyi/mill/releases/download/0.7.3/0.7.3 > /usr/local/bin/mill
chmod +x /usr/local/bin/mill
```

## 3. Install Java 8 and Set Default Version

```shell
sudo apt install openjdk-8-jdk-headless
#optional
sudo update-alternatives --config java
sudo update-alternatives --config javac
```

## 3. Scala Kernel for `Jupyter` (optional)

If you're new to Chisel, then maybe you can start at Chisel-Bootcamp, the useful and official Chisel tutorial, [online](https://mybinder.org/v2/gh/freechipsproject/chisel-bootcamp/master) or try it [locally](https://github.com/freechipsproject/chisel-bootcamp). and I translated module 3 to Chinese, you can clone it [at my repo](https://github.com/SingularityKChen/chisel-bootcamp/tree/zh_cn). And then you need to add a Scala Kernel to your `Jupyter`.

```shell
curl -L -o coursier https://git.io/coursier && chmod +x coursier
SCALA_VERSION=2.12.8 ALMOND_VERSION=0.2.1
./coursier bootstrap -r jitpack -i user -I user:sh.almond:scala-kernel-api_$SCALA_VERSION:$ALMOND_VERSION sh.almond:scala-kernel_$SCALA_VERSION:$ALMOND_VERSION --sources --default=true -o almond
./almond --install
```

After you have added the Scala kernel, then you can go to [this section](#Bootcamp (optional)) to see some steps of config `Chisel-Bootcamp`.

# Clone Chisel Environments (after sbt)

## 1. Chipyard

### QEMU Dependence

Two Errors I met when I tried to [install the RISC-V tools](#Installing the RISC-V Tools):

```shell
==>  Configuring qemu

ERROR: glib-2.48 gthread-2.0 is required to compile QEMU

ERROR: pixman >= 0.21.8 not present.
       Please install the pixman devel package.
```

So we need to install the lib before installing the RISC-V tools:

```shell
sudo apt-get install libglib2.0-dev libpixman-1-dev
```

### Setup

```shell
git clone https://github.com/ucb-bar/chipyard.git
cd chipyard
./scripts/init-submodules-no-riscv-tools.sh
```

### Installing the RISC-V Tools

The shell needs `bash` or you have to correct some commands in `env.sh`
```shell
./scripts/build-toolchains.sh
source ./env.sh
```

## 2. Firrtl

You don't need to clone it again if you have cloned `chipyard`. Just go to the directory `~/chipyard/tools/firrtl`, and `sbt publishLocal`.

```shell
cd ~
sudo git clone https://github.com/freechipsproject/firrtl.git && cd firrtl
sudo sbt compile
sudo sbt test
sudo sbt assembly
sudo sbt publishLocal
```

## 3. Chisel3

You don't need to clone it again if you have cloned `chipyard`. Just go to the directory `~/chipyard/tools/chisel3`, and `sbt publishLocal`.

```shell
cd ~
sudo git clone https://github.com/freechipsproject/chisel3.git && cd chisel3
sudo sbt compile
sudo sbt test
sudo sbt publishLocal
```

## 4. Bootcamp (optional)

```shell
git clone https://github.com/freechipsproject/chisel-bootcamp.git
cd chisel-bootcamp
mkdir -p ~/.jupyter/custom
cp source/custom.js ~/.jupyter/custom/custom.js
```
