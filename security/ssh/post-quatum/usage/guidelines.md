# Guidelines for Using Post-Quantum SSH Keys

## Overview
To ensure secure and efficient use of post-quantum SSH keys, Syslogine Aegis has developed these guidelines. By adhering to these practices, teams and collaborators can maintain enterprise-grade security and ensure compliance with our standards.

## General Guidelines

### 1. Use Strong Passphrases
Always secure your private SSH keys with a strong passphrase:
- Use at least 16 characters.
- Include a mix of uppercase, lowercase, numbers, and special characters.
- Avoid predictable phrases or dictionary words.

### 2. Restrict Key Permissions
Ensure the private key file has appropriate permissions:
```bash
chmod 600 ~/.ssh/id_dilithium
```
This prevents unauthorized access to your private key.

### 3. Avoid Key Sharing
Never share your private key with others. Use the public key (`~/.ssh/id_dilithium.pub`) for access provisioning.

### 4. Use Separate Keys for Different Systems
Avoid reusing the same key pair across multiple systems. Generate unique key pairs for each system or environment.

### 5. Rotate Keys Regularly
To minimize security risks, rotate your SSH keys every 6–12 months. Refer to the [Key Rotation Guide](../maintenance/key-rotation.md).

## Usage in Collaborative Environments

### 1. Register Public Keys Correctly
When sharing your public key with administrators or collaborators:
- Verify the recipient's identity.
- Use secure methods for transferring the public key (e.g., encrypted email or secure file transfer).

### 2. Use Key Comments
Include comments in your public keys to identify their purpose:
```bash
ssh-keygen -t dilithium3 -C "user@syslogine-aegis"
```

### 3. Limit Access Scope
Where possible, restrict the scope of access provided by your key. For example, in `~/.ssh/authorized_keys` on the server, prepend commands or restrictions:
```plaintext
command="/usr/bin/rsync" dilithium3 AAAA... user@syslogine-aegis
```

### 4. Monitor and Audit Key Usage
Regularly review server logs to ensure no unauthorized access is occurring:
```bash
tail -f /var/log/auth.log
```

## Troubleshooting
- **Authentication Failures**: Verify that the server supports the post-quantum algorithm in use.
- **Passphrase Issues**: Use an SSH agent to cache your passphrase securely:
  ```bash
  ssh-add ~/.ssh/id_dilithium
  ```

## Next Steps
- [Troubleshooting Common Issues](troubleshooting.md)
- [Key Rotation and Maintenance](../maintenance/key-rotation.md)

By following these guidelines, you can ensure secure and efficient usage of post-quantum SSH keys within Syslogine Aegis workflows.
