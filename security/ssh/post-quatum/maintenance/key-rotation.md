# Key Rotation for Post-Quantum SSH Keys

## Overview
Regularly rotating your SSH keys is a critical security practice to minimize the risk of key compromise. This guide provides a step-by-step process to safely rotate post-quantum SSH keys without disrupting access. Syslogine Aegis recommends **Dilithium** as the standard for post-quantum key management.

## Why Rotate Keys?
- Mitigates the risk of unauthorized access due to key compromise.
- Ensures compliance with Syslogine Aegis security policies.
- Adapts to updated cryptographic standards and algorithms.

## Key Rotation Process

### 1. Generate a New Key Pair
Create a new post-quantum SSH key pair on your local system:
```bash
oqs-keygen -a dilithium3 -o ~/.ssh/id_dilithium_new
```
**Note:** Use a unique filename to avoid overwriting your existing key.

### 2. Add the New Public Key to the Server
Copy the new public key to the server:
```bash
ssh-copy-id -i ~/.ssh/id_dilithium_new.pub user@hostname
```
Alternatively, manually append the public key to the server’s `~/.ssh/authorized_keys` file:
```bash
cat ~/.ssh/id_dilithium_new.pub >> ~/.ssh/authorized_keys
```

### 3. Test the New Key
Verify that the new key works before removing the old one:
```bash
ssh -i ~/.ssh/id_dilithium_new user@hostname
```
If the connection is successful, proceed to the next step.

### 4. Remove the Old Key
Once you confirm the new key is operational, remove the old key from the server:
```bash
nano ~/.ssh/authorized_keys
```
Delete the line containing the old public key, then save and exit.

### 5. Update Your Local SSH Configuration
Update your SSH agent to use the new key:
```bash
ssh-add -D
ssh-add ~/.ssh/id_dilithium_new
```
Optionally, update your `~/.ssh/config` file:
```plaintext
Host hostname
    HostName hostname
    User user
    IdentityFile ~/.ssh/id_dilithium_new
```

### 6. Securely Store and Backup Keys
- Save the new private key in a secure, encrypted location.
- Delete the old private key from your system:
  ```bash
  rm ~/.ssh/id_dilithium
  ```

## Best Practices
- Schedule key rotations every 6–12 months.
- Use unique keys for different servers or environments.
- Maintain a record of active keys and their expiration dates.

## Troubleshooting
- If the new key does not work, recheck the `authorized_keys` file for correct formatting.
- Use verbose mode to debug connection issues:
  ```bash
  ssh -vv -i ~/.ssh/id_dilithium_new user@hostname
  ```

## Related Documents
- [Generating Post-Quantum SSH Keys](../setup/generate-keys.md)
- [Configuring SSH for Post-Quantum Security](../setup/config.md)
- [Troubleshooting Key Issues](../usage/troubleshooting.md)

By following this guide, you can ensure a smooth and secure key rotation process while maintaining compliance with Syslogine Aegis security standards.
