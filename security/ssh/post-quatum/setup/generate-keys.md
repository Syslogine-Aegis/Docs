# Generating Post-Quantum SSH Keys

## Overview
This document was developed by Syslogine Aegis to provide step-by-step instructions for generating post-quantum SSH keys, ensuring secure and quantum-resistant communications as part of our commitment to enterprise-grade security solutions.

## Prerequisites
Before you begin, ensure the following:

1. **Operating System**: Debian 12 or compatible.
2. **SSH Version**: OpenSSH 9.0 or later, supporting post-quantum algorithms.
3. **Tools Installed**: Access to the `ssh-keygen` utility and `liboqs-dev` for advanced algorithms.

## Steps to Generate Keys

### 1. Verify OpenSSH Version
Ensure you are running a compatible version of OpenSSH:
```bash
ssh -V
```
The output should indicate OpenSSH version 9.0 or later.

### 2. Generate a Dilithium SSH Key Pair
Syslogine Aegis recommends using Dilithium as the default algorithm. Run the following command:
```bash
oqs-keygen -a dilithium3 -o ~/.ssh/id_dilithium
```
**Explanation:**
- `-a dilithium3`: Specifies the Dilithium-3 algorithm.
- `-o ~/.ssh/id_dilithium`: Sets the output file for the key pair.

### 3. Protect Your Private Key
Set appropriate permissions to secure your private key:
```bash
chmod 600 ~/.ssh/id_dilithium
```

### 4. Add the Key to Your SSH Agent
To simplify usage, add the private key to your SSH agent:
```bash
ssh-add ~/.ssh/id_dilithium
```

### 5. Share Your Public Key
The public key is stored in `~/.ssh/id_dilithium.pub`. Share this key with the appropriate server administrator to allow access:
```bash
cat ~/.ssh/id_dilithium.pub
```

## Key Management Best Practices
1. Rotate keys every 6–12 months.
2. Use a strong passphrase for additional protection.
3. Store backups securely in an encrypted location.

## Troubleshooting
If you encounter any issues, refer to the [troubleshooting guide](../usage/troubleshooting.md).

## Next Steps
- [Configure SSH for Post-Quantum Security](config.md)
- [Install Required Tools](install-tools.md)

```