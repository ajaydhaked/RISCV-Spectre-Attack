## Table of Contents

* [Prerequisites](#prerequisites)
* [Cloning the Repo](#cloning-the-repo)
* [Install gem5 dependencies](#install-gem5-dependencies)
* [Build gem5 Executable](#build-gem5-executable)
* [Build RISC-V Cross Compiler](#build-risc-v-cross-compiler)
* [Compile Spectre v1 attack code](#compile-spectre-v1-attack-code)
* [System Configuration](#system-configuration)
* [Run Spectre v1 attack](#run-spectre-v1-attack)

---

## Prerequisites

* Python 3.6 or higher
* PC or virtual machine running Ubuntu 20.04 or higher
* Root access

---

## Cloning the Repo

Clone the following repo to download all sample code:

```shell
git clone https://github.com/ajaydhaked/RISCV-Spectre-Attack.git
```

---

## Install gem5 dependencies

```shell
sudo apt install build-essential git m4 scons zlib1g zlib1g-dev \
    libprotobuf-dev protobuf-compiler libprotoc-dev libgoogle-perftools-dev \
    python3-dev libboost-all-dev pkg-config python3-tk clang-format-15
```

---

## Build gem5 Executable

```shell
cd gem5
scons build/RISCV/gem5.opt -j$(nproc)
```

---

## Build RISC-V Cross Compiler

```shell
sudo apt-get install autoconf automake autotools-dev curl python3 python3-pip python3-tomli \
libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf \
libtool patchutils bc zlib1g-dev libexpat-dev ninja-build git cmake \
libglib2.0-dev libslirp-dev libncurses-dev
```

Clone the toolchain:

```shell
git clone https://github.com/riscv/riscv-gnu-toolchain
cd riscv-gnu-toolchain
```

Configure:

```shell
./configure --prefix=/opt/riscv
```

Build and export:

```shell
sudo make linux
export PATH=$PATH:/opt/riscv/bin/
```

---

## Compile Spectre v1 attack code

```shell
cd v1_attack
riscv64-unknown-linux-gnu-gcc ./v1_attack/spectre_working.c -o spectre_working -static
```

---

## System Configuration

The simulation uses a custom gem5 configuration with the following architecture:

### CPU

* **Model:** DerivO3CPU (Out-of-Order CPU)
* **Clock Frequency:** 1 GHz

### Memory System

* **Memory Mode:** Timing
* **Memory Size:** 512 MB
* **DRAM Type:** DDR3_1600_8x8

### Cache Hierarchy

* **L1 Instruction Cache:** Private per core
* **L1 Data Cache:** Private per core
* **L2 Cache:** Shared unified cache

### Interconnect

* **L1 → L2:** L2 Crossbar (L2XBar)
* **L2 → Memory:** System Crossbar (SystemXBar)

### Execution Mode

* **Mode:** Syscall Emulation (SE Mode)
* **Workload:** User-provided RISC-V binary

### Key Features

* Two-level cache hierarchy (L1 + L2)
* Out-of-order execution core
* Timing-based memory accesses
* Configurable cache sizes via script arguments

---

## Run Spectre v1 attack RISC-V OoO core

```shell
./gem5/build/RISCV/gem5.opt \
./gem5/configs/learning_gem5/part1/two_level.py \
./v1_attack/spectre_working
```

---

## Notes

* The configuration is based on gem5's learning examples and modified for RISC-V OoO execution.
* The setup is suitable for studying microarchitectural attacks like Spectre v1.
