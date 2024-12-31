# Generating Post-Quantum SSH Keys

## Overview
This document was developed by Syslogine Aegis to provide step-by-step instructions for generating post-quantum SSH keys, ensuring secure and quantum-resistant communications as part of our commitment to enterprise-grade security solutions.

## Prerequisites
Before you begin, ensure you have the following:

1. **Operating System**: Debian 12 or a compatible Linux distribution
2. **SSH Version**: OpenSSH 9.0 or later, supporting post-quantum algorithms
3. **Installed Tools**:
   - `ssh-keygen` utility
   - `liboqs-dev` (version 0.7.0 or later) for advanced algorithms
   - `openssl` (version 3.0 or later)
   - `oqs-tools` (optional, for additional post-quantum features)

## Table of Contents
1. [Verify OpenSSH and OpenSSL Versions](#verify-openssh-and-openssl-versions)
2. [Install Required Tools](#install-required-tools)
3. [Generate a Post-Quantum SSH Key Pair](#generate-a-post-quantum-ssh-key-pair)
4. [Secure Your Private Key](#secure-your-private-key)
5. [Add the Key to Your SSH Agent](#add-the-key-to-your-ssh-agent)
6. [Share Your Public Key](#share-your-public-key)
7. [Key Management Best Practices](#key-management-best-practices)
8. [Automate Key Rotation](#automate-key-rotation)
9. [Troubleshooting](#troubleshooting)
10. [Testing and Validation](#testing-and-validation)
11. [References](#references)
12. [Next Steps](#next-steps)
13. [Version Control](#version-control)

## Verify OpenSSH and OpenSSL Versions
Ensure you are using the correct versions of OpenSSH and OpenSSL that support post-quantum algorithms.

```bash
ssh -V
openssl version
```

The output should indicate **OpenSSH version 9.0 or later** and **OpenSSL version 3.0 or later**. Consult your specific OpenSSH version documentation to confirm post-quantum support.

## Install Required Tools
Install the necessary tools if they are not already installed:

```bash
sudo apt update
sudo apt install -y liboqs-dev openssl libssl-dev
```

**Optional:** For additional post-quantum features, install `oqs-tools`:

```bash
sudo apt install -y oqs-tools
```

## Generate a Post-Quantum SSH Key Pair
Syslogine Aegis recommends using **Dilithium** as the default algorithm due to its strong security properties and performance characteristics. Additionally, you can consider other NIST-approved algorithms such as **Falcon** or **Sphincs+**.

### 1. Choose a Post-Quantum Algorithm
Available algorithms:
- **Dilithium3**
- **Falcon-512**
- **Sphincs+-SHA256-128F-robust**

### 2. Generate the Key
Use `oqs-keygen` to generate a key. Replace `<algorithm>` with your chosen algorithm.

```bash
oqs-keygen -a <algorithm> --security-level=3 -o ~/.ssh/id_<algorithm>_$(date +%Y%m%d)
```

**Example with Dilithium3:**

```bash
oqs-keygen -a dilithium3 --security-level=3 -o ~/.ssh/id_dilithium3_$(date +%Y%m%d)
```

**Explanation:**
- `-a <algorithm>`: Specifies the post-quantum algorithm
- `--security-level=3`: Sets NIST security level 3 for enhanced protection
- `-o ~/.ssh/id_<algorithm>_$(date +%Y%m%d)`: Sets the output file with a date stamp for tracking

## Secure Your Private Key
Set appropriate permissions to secure your private key:

```bash
chmod 600 ~/.ssh/id_<algorithm>_*
chmod 644 ~/.ssh/id_<algorithm>_*.pub
```

**Replace `<algorithm>` with your chosen algorithm, e.g., `dilithium3`.**

## Add the Key to Your SSH Agent
To enhance usability, add the private key to your SSH agent:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_<algorithm>_*
```

### Additional Security for the SSH Agent
Consider the following configurations for added security:
- **Set a time-out** for inactive sessions:
  ```bash
  ssh-add -t 3600 ~/.ssh/id_<algorithm>_*
  ```
  *(Here, `3600` sets the timeout to 1 hour)*
  
- **Limit key usage** by configuring SSH-agent forwarding restrictions in your SSH configuration.

## Share Your Public Key
The public key is stored with a `.pub` extension. Share this key with the appropriate server administrator to grant access:

```bash
cat ~/.ssh/id_<algorithm>_*.pub
```

## Key Management Best Practices
1. **Key Rotation**:
   - **Rotation Interval**: Rotate keys every 90 days.
   - **Automation**: Use scripts to automate the rotation process (see [Automate Key Rotation](#automate-key-rotation) section).
   
2. **Strong Passphrases**:
   - Use a passphrase of at least 16 characters, combining uppercase letters, lowercase letters, numbers, and symbols.
   
3. **Secure Backups**:
   - Store backups in an encrypted location with restricted access.
   
4. **Documentation**:
   - Document all key operations in a secure log.
   
5. **Regular Verification**:
   - Regularly verify the integrity and permissions of your keys.

## Automate Key Rotation
Manually rotating keys can be time-consuming. Below is a simple script to automate key rotation:

```bash
#!/bin/bash

ALGO=$1
KEY_DIR=~/.ssh
DATE=$(date +%Y%m%d)

# Generate a new key
oqs-keygen -a $ALGO --security-level=3 -o $KEY_DIR/id_${ALGO}_$DATE

# Secure key files
chmod 600 $KEY_DIR/id_${ALGO}_*
chmod 644 $KEY_DIR/id_${ALGO}_*.pub

# Add to SSH agent
ssh-add $KEY_DIR/id_${ALGO}_*

# Remove old keys (older than 90 days)
find $KEY_DIR -name "id_${ALGO}_*" -type f -mtime +90 -exec rm {} \;

echo "Key rotation for $ALGO completed on $DATE."
```

**Usage:**
```bash
./rotate_keys.sh dilithium3
```

**Note:** Ensure the script is executable:
```bash
chmod +x rotate_keys.sh
```

## Troubleshooting
If you encounter issues, follow these steps:

1. **Check Prerequisites**:
   - Ensure all required tools are correctly installed and up to date.

2. **Verify Versions**:
   - Confirm you are using the correct versions of OpenSSH and OpenSSL.

3. **Review System Logs**:
   - Check system logs for error messages:
     ```bash
     sudo journalctl -xe
     ```

4. **Verify Permissions**:
   - Ensure key file permissions are correctly set.

5. **Check Compatibility**:
   - Confirm that `liboqs` and OpenSSH are correctly integrated for post-quantum support.

6. **Resolve Specific Errors**:
   - **Issue**: `oqs-keygen` not found
     - **Solution**: Ensure `oqs-tools` is correctly installed and in your PATH.
   
   - **Issue**: SSH agent does not accept the post-quantum key
     - **Solution**: Verify that OpenSSH supports post-quantum algorithms in your version. Refer to official documentation or consider applying necessary patches.

7. **Refer to Troubleshooting Guide**:
   - Consult the [troubleshooting guide](../usage/troubleshooting.md) for more detailed solutions.

## Testing and Validation
Before deploying the generated keys in production, test them in a controlled environment:

1. **Test Connection**:
   - Establish an SSH connection with a test server to confirm the key works:
     ```bash
     ssh -i ~/.ssh/id_<algorithm>_<date> user@testserver
     ```

2. **Verify Key Integrity**:
   - Use `ssh-keygen` to verify the integrity of the key:
     ```bash
     ssh-keygen -lf ~/.ssh/id_<algorithm>_<date>
     ```

3. **Check Compatibility**:
   - Ensure both the client and server support and are correctly configured for the post-quantum algorithms.

## References
- [NIST Post-Quantum Cryptography](https://csrc.nist.gov/projects/post-quantum-cryptography)
- [OpenSSH Official Documentation](https://www.openssh.com/manual.html)
- [liboqs GitHub Repository](https://github.com/open-quantum-safe/liboqs)
- [OQS-OpenSSH Project](https://github.com/open-quantum-safe/openssh)

## Next Steps
- [Configure SSH for Post-Quantum Security](config.md)
- [Install Required Tools](install-tools.md)
- [Key Management Policy](key-policy.md)

## Version Control
- **Document Version**: 1.2
- **Last Updated**: 2024-12-31
- **Review Cycle**: Quarterly

---

**Note:** Stay informed about the latest developments in post-quantum cryptography and regularly update your processes and tools to maintain the highest security standards.