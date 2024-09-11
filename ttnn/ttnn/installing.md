# Install

These instructions will guide you through the installation of Tenstorrent system tools and drivers, followed by the installation of TT-Metalium and TT-NN.

---

## Installation Steps

### Step 1. Driver & Firmware

Follow the Software Setup instructions for your specific board or system provided on our [general docs](https://docs.tenstorrent.com/tenstorrent).

If you have purchased a Grayskull card, you will find the instructions [here](https://docs.tenstorrent.com/tenstorrent/add-in-boards-and-cooling-kits/grayskull-tm-e75-e150/software-setup).

Note the current compatability matrix:

| Device              | OS              | Python   | Driver (TT-KMD)    | Firmware (TT-Flash)                        | TT-SMI                | TT-Topology                    |
|---------------------|-----------------|----------|--------------------|--------------------------------------------|-----------------------|--------------------------------|
| Grayskull           | Ubuntu 20.04    | 3.8.10   | v1.26              | fw_pack-80.4.0.0_acec1267.tar.gz (v4.0.0)  | v2.1.0 or above       | N/A                            |
| Wormhole            | Ubuntu 20.04    | 3.8.10   | v1.26              | fw_pack-80.8.11.0.tar.gz (v80.8.11.0)      | v2.1.0 or above       | N/A                            |
| T3000 (Wormhole)    | Ubuntu 20.04    | 3.8.10   | v1.26              | fw_pack-80.8.11.0.tar.gz (v80.8.11.0)      | v2.1.0 or above       | v1.0.2 or above, `mesh` config |

---

### Step 2. System-level dependencies

```sh
sudo apt update
sudo apt install software-properties-common=0.99.9.12 build-essential=12.8ubuntu1.1 python3.8-venv=3.8.10-0ubuntu1~20.04.9 libhwloc-dev graphviz patchelf

wget https://apt.llvm.org/llvm.sh
chmod u+x llvm.sh
sudo ./llvm.sh 17
sudo apt install libc++-17-dev libc++abi-17-dev
```

---

### Step 3. Hugepages

1. Download latest [setup_hugepages.py](https://github.com/tenstorrent/tt-metal/blob/main/infra/machine_setup/scripts/setup_hugepages.py) script.

```sh
wget https://raw.githubusercontent.com/tenstorrent/tt-metal/main/infra/machine_setup/scripts/setup_hugepages.py
```

2. Run first setup script.

```sh
sudo -E python3 setup_hugepages.py first_pass
```

3. Reboot

```sh
sudo reboot now
```

4. Run second setup script & check setup.

```sh
sudo -E python3 setup_hugepages.py enable && sudo -E python3 setup_hugepages.py check
```
---

### Step 4. Install and start using TT-NN and TT-Metalium!

> [!NOTE]
>
> You may choose to install from either source or a Python wheel.
>
> However, no matter your method, in order to use our pre-built models or to
> follow along with the documentation and tutorials to get started, you will
> still need the source code.
>
> If you do not want to use the models or follow the tutorials and want to
> immediately start using the API, you may install just the wheel.

1. Install git and git-lfs

```sh
sudo apt install git git-lfs
```

2. Clone the repo.

```sh
git clone https://github.com/tenstorrent/tt-metal.git --recurse-submodules
cd tt-metal
git submodule foreach 'git lfs fetch --all && git lfs pull'
```

3. Set up the environment variables. For Grayskull, use:

```sh
export ARCH_NAME=grayskull
export TT_METAL_HOME=$(pwd)
export PYTHONPATH=$(pwd)
```

For Wormhole boards, use:

```sh
export ARCH_NAME=wormhole_b0
export TT_METAL_HOME=$(pwd)
export PYTHONPATH=$(pwd)
```

4. Install either from source, or from our release wheel.

### Option 1: From source

We use CMake for our build flows.

```sh
./build_metal.sh

# If you would like an out-of-the-box virtual environment to use,
./create_venv.sh
source python_env/bin/activate
```

Note about Python environments: You do not have to use `create_venv.sh`. If you
are less familiar with Python and its various environment tools, just use
`create_venv.sh` as shown above and the pre-built environment.

### Option 2: From wheel

Download the latest wheel from our
[releases](https://github.com/tenstorrent/tt-metal/releases/latest) page for
the particular Tenstorrent card architecture that you have installed on your
system. (ie. Grayskull, Wormhole, etc)

Install the wheel using your Python environment manager of choice. For example,
to install with `pip`:

```sh
pip install <wheel_file.whl>
```

If you are going to try our pre-built models in `models/`, then you must also
further install their requirements:

```sh
pip install -r tt_metal/python_env/requirements-dev.txt
```

5. Start coding

You are all set! Visit the [TT-NN Basic examples page](https://tenstorrent.github.io/tt-metal/latest/ttnn/ttnn/usage.html#basic-examples) or get started with [simple kernels on TT-Metalium](https://tenstorrent.github.io/tt-metal/latest/tt-metalium/tt_metal/examples/index.html).

---

### Step 5. (Optional) Software dependencies for codebase contributions

Please follow the next additional steps if you want to contribute to the codebase.

1. Install dependencies

```sh
sudo apt install cmake=3.16.3-1ubuntu1.20.04.1 pandoc libtbb-dev libcapstone-dev pkg-config ninja-build
```

2. Download and install [Doxygen](https://www.doxygen.nl/download.html), (v1.9 or higher, but less than v1.10)
