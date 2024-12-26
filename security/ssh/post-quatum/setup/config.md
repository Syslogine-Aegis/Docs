# Configuring SSH for Post-Quantum Security

## Overview
This guide outlines the steps required to configure SSH to utilize post-quantum cryptographic algorithms for secure communication. Syslogine Aegis recommends **Dilithium** as the default algorithm for digital signatures.

## Prerequisites
Before proceeding, ensure:

1. Post-quantum SSH keys have been generated. Refer to [Generating Post-Quantum SSH Keys](generate-keys.md).
2. Access to the SSH server configuration file (`/etc/ssh/sshd_config`) on the target server.
3. Administrative privileges on the SSH server.

## Steps to Configure SSH

### 1. Backup Existing Configuration Files
Before making any changes, create a backup of the existing SSH configuration:
```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
```

### 2. Update the SSH Server Configuration
Edit the `sshd_config` file to prioritize **Dilithium** and other post-quantum cryptographic algorithms:
```bash
sudo nano /etc/ssh/sshd_config
```

Add or modify the following lines:
```plaintext
HostKeyAlgorithms +dilithium3
PubkeyAcceptedAlgorithms +dilithium3
KexAlgorithms +curve25519-sha256@libssh.org
```

**Explanation:**
- `HostKeyAlgorithms`: Adds support for Dilithium as the host key algorithm.
- `PubkeyAcceptedAlgorithms`: Ensures the server accepts Dilithium public key algorithms.
- `KexAlgorithms`: Configures the key exchange algorithm to use secure post-quantum methods.

### 3. Test the Configuration
Before restarting the SSH service, test the updated configuration:
```bash
sudo sshd -t
```
If no errors are reported, proceed to the next step.

### 4. Restart the SSH Service
Apply the configuration changes by restarting the SSH service:
```bash
sudo systemctl restart sshd
```

### 5. Update SSH Client Configuration (Optional)
To enforce post-quantum algorithms on the client side, update the `~/.ssh/config` file:
```bash
nano ~/.ssh/config
```
Add the following lines:
```plaintext
Host *
    HostKeyAlgorithms +dilithium3
    PubkeyAcceptedAlgorithms +dilithium3
    KexAlgorithms +curve25519-sha256@libssh.org
```

## Verification
1. Connect to the server using your post-quantum key:
   ```bash
   ssh -i ~/.ssh/id_dilithium user@hostname
   ```
2. Verify the algorithm used for the connection:
   ```bash
   ssh -vv user@hostname
   ```
   Look for `HostKeyAlgorithms` and `KexAlgorithms` in the output.

## Key Points to Remember
- Regularly audit your SSH configuration to ensure compliance with the latest security standards.
- Rotate keys periodically and review accepted algorithms for deprecation notices.

## Next Steps
- [Key Rotation and Auditing](maintenance/key-rotation.md)
