# Generating Post-Quantum SSH Keys

## Overview
This document was developed by Syslogine Aegis to provide step-by-step instructions for generating post-quantum SSH keys, ensuring secure and quantum-resistant communications as part of our commitment to enterprise-grade security solutions.

## Prerequisites
Before you begin, ensure the following:

1. **Operating System**: Debian 12 or compatible.
2. **SSH Version**: OpenSSH 9.0 or later, supporting post-quantum algorithms.
3. **Tools Installed**: Access to the `ssh-keygen` utility and `liboqs-dev` for advanced algorithms.

## Supported Algorithms
Syslogine Aegis recommends **Dilithium** as the default algorithm for its robustness and scalability. Additionally, the following algorithms are supported:

- [**FALCON**](algorithms/falcon.md): Lightweight and efficient digital signatures.
- [**Kyber**](algorithms/kyber.md): High-performance key encapsulation mechanism.
- [**SPHINCS+**](algorithms/sphincs-plus.md): Stateless hash-based signatures for maximum security.
- [**BIKE**](algorithms/bike.md): Compact and efficient key encapsulation.
- [**FrodoKEM**](algorithms/frodo-kem.md): Secure key encapsulation based on LWE.
- [**ED25519**](algorithms/ed25519.md): Efficient elliptic-curve signatures (not post-quantum but widely supported).
- [**Hybrid algorithms**](algorithms/hybrid-algorithms.md): Combining post-quantum and classical methods (e.g., `ECDSA + Dilithium`).

## Steps to Generate Post-Quantum SSH Keys

### 1. System Preparation and Verification

First, verify your OpenSSH installation and environment:

```bash
# Check OpenSSH version
ssh -V

# Verify OQS toolkit installation
oqs-keygen --version

# Ensure SSH directory exists with correct permissions
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

### 2. Generate Post-Quantum Keys

#### Standard Dilithium Key Generation
```bash
# Generate Dilithium-3 keys with maximum entropy
oqs-keygen -a dilithium3 -o ~/.ssh/id_dilithium3 -f

# Parameters explained:
# -a dilithium3    : Uses NIST Level 3 security
# -o               : Specifies output location
# -f               : Forces key generation even if file exists
```

#### Alternative: Generate Hybrid Keys (Optional)
```bash
# Generate hybrid keys combining classical and quantum resistance
oqs-keygen -a dilithium3+ed25519 -o ~/.ssh/id_hybrid -f
```

### 3. Secure Key Permissions

Set proper filesystem permissions to protect your keys:

```bash
# Secure private key
chmod 600 ~/.ssh/id_dilithium3

# Set public key permissions
chmod 644 ~/.ssh/id_dilithium3.pub

# Verify permissions
ls -la ~/.ssh/id_dilithium3*
```

### 4. Configure SSH Agent

Set up and configure your SSH agent for key management:

```bash
# Start SSH agent if not running
eval $(ssh-agent)

# Add private key with custom lifetime
ssh-add -t 12h ~/.ssh/id_dilithium3

# Verify key was added
ssh-add -l
```

### 5. Key Distribution and Testing

#### 5.1 Export Public Key
```bash
# Display public key for copying
cat ~/.ssh/id_dilithium3.pub

# Copy public key to clipboard (Linux)
cat ~/.ssh/id_dilithium3.pub | xclip -selection clipboard

# Alternative: Create a transferable file
cat ~/.ssh/id_dilithium3.pub > ~/dilithium3_key.pub
```

#### 5.2 Test Key Generation
```bash
# Verify key integrity
ssh-keygen -l -f ~/.ssh/id_dilithium3.pub

# Test key functionality (replace user@remote-host)
ssh -i ~/.ssh/id_dilithium3 -o IdentitiesOnly=yes user@remote-host
```

### 6. Backup Keys (Recommended)

Create encrypted backups of your keys:

```bash
# Create encrypted archive of keys
tar czf - ~/.ssh/id_dilithium3* | gpg -c > quantum_keys_backup.tar.gz.gpg

# Store backup in secure location
mv quantum_keys_backup.tar.gz.gpg /secure/backup/location/
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
