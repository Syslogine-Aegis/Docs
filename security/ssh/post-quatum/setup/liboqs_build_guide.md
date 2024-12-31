# Installing liboqs on Kali Linux - Build Guide

This guide explains how to build and install liboqs from source on Kali Linux.

## Prerequisites

First, install the required build tools:

```bash
sudo apt update
sudo apt install build-essential cmake ninja-build automake autoconf git python3 pkg-config
```

## Building Steps

1. Clone the liboqs repository:
```bash
git clone https://github.com/open-quantum-safe/liboqs.git
cd liboqs
```

2. Create and enter the build directory:
```bash
mkdir build
cd build
```

3. Configure the build using cmake:
```bash
cmake -GNinja ..
```

4. Compile the code:
```bash
ninja
```

5. Run tests (optional but recommended):
```bash
ninja run_tests
```

6. Install the library:
```bash
sudo ninja install
```

7. Update the shared library cache:
```bash
sudo ldconfig
```

## Verification

To verify that the library is correctly installed, run:
```bash
pkg-config --modversion liboqs
```

This should display the installed version number.

## Installation Details

The installation includes:
- Shared libraries (`.so` files)
- Header files (`.h` files)
- Development files for linking
- Documentation

The header files will be installed in `/usr/local/include/oqs`. You can verify this by running:
```bash
ls /usr/local/include/oqs
```

## Note

This build method includes all development files (equivalent to the `-dev` package in apt repositories), so no additional packages need to be installed for development purposes.

---
*Feel free to contribute to this guide by submitting issues or pull requests.*