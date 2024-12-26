# Auditing SSH Configurations and Key Usage

## Overview
Regular audits of your SSH configurations and key usage are essential for maintaining security and compliance with Syslogine Aegis standards. This document provides a step-by-step guide to auditing SSH configurations and post-quantum key usage.

## Objectives
- Identify unauthorized access or misconfigurations.
- Ensure compliance with post-quantum cryptographic standards.
- Mitigate risks associated with outdated or unused keys.

## Audit Checklist

### 1. Review `sshd_config`
Check the SSH server configuration file for compliance:
```bash
sudo nano /etc/ssh/sshd_config
```
Ensure the following:
- Post-quantum algorithms are enabled:
  ```plaintext
  HostKeyAlgorithms +dilithium3
  PubkeyAcceptedAlgorithms +dilithium3
  KexAlgorithms +curve25519-sha256@libssh.org
  ```
- Unused or deprecated algorithms are disabled:
  ```plaintext
  HostKeyAlgorithms -ssh-dss
  PubkeyAcceptedAlgorithms -ssh-rsa
  ```

Restart the SSH service after making changes:
```bash
sudo systemctl restart sshd
```

### 2. Verify Key Usage
Identify active SSH keys and their associated users:
```bash
tail -f /var/log/auth.log | grep 'Accepted publickey'
```
Analyze:
- Which keys are frequently used.
- If there are any unexpected logins.

### 3. Validate Key Rotation
Check for recently rotated keys:
- Review the `~/.ssh/authorized_keys` file for unused or expired keys.
- Confirm that old keys are removed and new keys are functional.

### 4. Monitor for Unauthorized Access
Search logs for failed login attempts:
```bash
sudo grep 'Failed password' /var/log/auth.log
```
Take note of any unusual activity and block suspicious IPs:
```bash
sudo ufw deny from <IP_ADDRESS>
```

### 5. Confirm Key Permissions
Ensure proper file permissions for SSH keys:
- Private key permissions:
  ```bash
  chmod 600 ~/.ssh/id_dilithium
  ```
- Public key permissions:
  ```bash
  chmod 644 ~/.ssh/id_dilithium.pub
  ```

### 6. Conduct Regular Key Reviews
Maintain an inventory of active keys and their associated users:
- List all keys:
  ```bash
  ls -l ~/.ssh/
  ```
- Periodically check with team members to confirm key ownership.

## Reporting Audit Findings
Create an audit report summarizing:
- Configuration compliance status.
- Key usage analysis.
- Unauthorized access attempts and mitigations.

### Example Report Template
| **Category**         | **Status**          | **Action Required**       |
|----------------------|---------------------|---------------------------|
| Post-Quantum Config  | Compliant           | None                      |
| Key Permissions      | Compliant           | None                      |
| Unauthorized Access  | 2 Attempts Blocked  | Continue Monitoring       |

## Automation Tools
To simplify auditing, consider using tools like:
- **SSH-Audit**: A command-line tool for auditing SSH servers:
  ```bash
  ssh-audit hostname
  ```
- **Fail2Ban**: Automatically block suspicious IPs.

## Next Steps
- [Key Rotation Guide](../maintenance/key-rotation.md)
- [Troubleshooting Common Issues](../usage/troubleshooting.md)

By following this guide, you can ensure a secure and well-managed SSH environment aligned with Syslogine Aegis security policies.