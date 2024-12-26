# Troubleshooting Post-Quantum SSH Key Issues

## Overview
This guide provides solutions to common issues encountered when using post-quantum SSH keys. By following these steps, you can resolve problems efficiently and maintain secure communication. Syslogine Aegis recommends prioritizing **Dilithium** for digital signatures as the standard post-quantum algorithm.

## Common Issues and Solutions

### 1. Authentication Failures
**Problem:** Unable to connect to the server using the post-quantum SSH key.

**Solution:**
- Verify the public key is correctly added to the server's `~/.ssh/authorized_keys` file.
- Ensure the correct key file is being used during authentication:
  ```bash
  ssh -i ~/.ssh/id_dilithium user@hostname
  ```
- Confirm the server supports the post-quantum algorithm by checking the `sshd_config` file:
  ```plaintext
  PubkeyAcceptedAlgorithms +dilithium3
  ```
- Restart the SSH service after updating the configuration:
  ```bash
  sudo systemctl restart sshd
  ```

### 2. Unsupported Algorithm Error
**Problem:** SSH client or server does not recognize the post-quantum algorithm.

**Solution:**
- Update OpenSSH to version 9.0 or later:
  ```bash
  sudo apt update && sudo apt install -y openssh-server openssh-client
  ```
- If using custom-built OpenSSH, ensure it includes post-quantum algorithm support (e.g., built with `liboqs`).

### 3. Permission Denied Errors
**Problem:** The private key permissions are incorrect.

**Solution:**
- Set proper permissions for the private key file:
  ```bash
  chmod 600 ~/.ssh/id_dilithium
  ```
- Ensure the key is readable only by the owner.

### 4. SSH Agent Issues
**Problem:** Unable to add the post-quantum key to the SSH agent.

**Solution:**
- Ensure the SSH agent is running:
  ```bash
  eval $(ssh-agent)
  ```
- Add the key to the agent:
  ```bash
  ssh-add ~/.ssh/id_dilithium
  ```
- Verify the key is added:
  ```bash
  ssh-add -l
  ```

### 5. Key Rotation Challenges
**Problem:** Difficulty rotating keys without losing access.

**Solution:**
- Add the new public key to the server before removing the old one.
- Test the new key to confirm access.
- Remove the old key from `~/.ssh/authorized_keys` only after verifying the new key works.

### 6. Debugging Connection Issues
**Problem:** General issues preventing SSH connection.

**Solution:**
- Use verbose mode to debug the connection:
  ```bash
  ssh -vv user@hostname
  ```
- Review the server logs for additional details:
  ```bash
  sudo tail -f /var/log/auth.log
  ```

## Additional Tips
- Ensure that the client and server time zones are synchronized to avoid timestamp mismatches.
- Regularly audit your SSH configurations to ensure compatibility with the latest standards.
- If using custom tools or libraries, refer to their documentation for compatibility notes.

## Support
For further assistance, please contact the Syslogine Aegis support team or open an issue in the repository.

## Related Documents
- [Generating Post-Quantum SSH Keys](../setup/generate-keys.md)
- [Configuring SSH for Post-Quantum Security](../setup/config.md)
- [Guidelines for Usage](guidelines.md)