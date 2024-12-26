# Installing Required Tools for Post-Quantum SSH

## Overview
To implement post-quantum cryptography in SSH, specific tools and dependencies must be installed on your system. This guide outlines the necessary installations and their setup process. Syslogine Aegis recommends using **Dilithium** as the default algorithm for post-quantum cryptography.

## Prerequisites
1. Access to a system running Debian 12 or compatible.
2. Administrative privileges (sudo access).

## Steps to Install Tools

### 1. Update Your System
Ensure your system is up to date before installing any packages:
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install OpenSSH
Verify that you have OpenSSH installed. Install or update it to version 9.0 or later:
```bash
sudo apt install -y openssh-server openssh-client
```
Check the installed version:
```bash
ssh -V
```

### 3. Install Quantum-Safe Libraries
Install libraries required for post-quantum cryptography:
```bash
sudo apt install -y liboqs-dev
```
**Note**: If `liboqs-dev` is unavailable in your repositories, you may need to build it from source. Refer to the [Open Quantum Safe Project](https://openquantumsafe.org/) for instructions.

### 4. Install SSH Utilities
For advanced configuration and testing, install additional SSH utilities:
```bash
sudo apt install -y sshpass
```

### 5. Configure Dilithium as Default
Ensure that **Dilithium** is prioritized as the default algorithm in your SSH configurations. Edit your `~/.ssh/config` file:
```bash
nano ~/.ssh/config
```
Add the following lines:
```plaintext
Host *
    HostKeyAlgorithms +dilithium3
    PubkeyAcceptedAlgorithms +dilithium3
```

### 6. Optional: Build OpenSSH with OQS Support
If your version of OpenSSH does not natively support post-quantum algorithms, you may need to compile OpenSSH with OQS support.

#### a. Install Dependencies
```bash
sudo apt install -y build-essential zlib1g-dev libssl-dev
```

#### b. Clone the OpenSSH Repository
```bash
git clone https://github.com/open-quantum-safe/openssh.git
cd openssh
```

#### c. Build and Install
```bash
./configure --with-liboqs
make
sudo make install
```

### 7. Verify Installation
Confirm that the required tools are installed and operational:
```bash
ssh -Q key
```
This command lists supported key types. Ensure `dilithium3` and other post-quantum algorithms are present.

## Troubleshooting
- **Missing Dependencies**: Ensure your repositories are configured correctly to access all required packages.
- **Compilation Errors**: Refer to the [Open Quantum Safe Documentation](https://openquantumsafe.org/documentation/) for build-specific issues.

## Next Steps
- [Generate Post-Quantum SSH Keys](generate-keys.md)
- [Configure SSH for Post-Quantum Security](config.md)
