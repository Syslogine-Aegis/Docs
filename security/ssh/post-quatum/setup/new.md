## **1. Verify OpenSSH and OpenSSL Versions**

Ensure that your system is running the required versions of OpenSSH and OpenSSL that support post-quantum algorithms.

```bash
ssh -V
openssl version
```

**Expected Output:**
- **OpenSSH version:** 9.0 or later
- **OpenSSL version:** 3.0 or later

---

## **2. Install Required Tools**

Update your package list and install the necessary tools for generating post-quantum SSH keys.

```bash
sudo apt update
sudo apt install -y liboqs-dev openssl libssl-dev
```

**Optional:** For additional post-quantum features, install `oqs-tools`.

```bash
sudo apt install -y oqs-tools
```

---

## **3. Generate a Post-Quantum SSH Key Pair**

Syslogine Aegis recommends using **Dilithium3** as the default algorithm. You can also choose other NIST-approved algorithms like **Falcon-512** or **Sphincs+-SHA256-128F-robust**.

**Example with Dilithium3:**

```bash
oqs-keygen -a dilithium3 --security-level=3 -o ~/.ssh/id_dilithium3_$(date +%Y%m%d)
```

**Explanation:**
- `-a dilithium3`: Specifies the Dilithium3 algorithm.
- `--security-level=3`: Sets the NIST security level to 3 for enhanced protection.
- `-o ~/.ssh/id_dilithium3_$(date +%Y%m%d)`: Outputs the key with a date stamp for tracking.

---

## **4. Secure Your Private Key**

Set appropriate permissions to ensure your private key is secure.

```bash
chmod 600 ~/.ssh/id_dilithium3_*
chmod 644 ~/.ssh/id_dilithium3_*.pub
```

---

## **5. Add the Key to Your SSH Agent**

Start the SSH agent and add your newly generated private key to it for easier usage.

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_dilithium3_*
```

**Optional:** Set a time-out for inactive sessions (e.g., 1 hour).

```bash
ssh-add -t 3600 ~/.ssh/id_dilithium3_*
```

---

## **6. Share Your Public Key**

Display your public key so you can share it with the appropriate server administrator.

```bash
cat ~/.ssh/id_dilithium3_*.pub
```

---

## **7. Automate Key Rotation**

Manually rotating keys can be time-consuming. Below is a script to automate the rotation process.

### **a. Create the Rotation Script**

Create a file named `rotate_keys.sh` and add the following content:

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

### **b. Make the Script Executable**

```bash
chmod +x rotate_keys.sh
```

### **c. Run the Rotation Script**

Replace `<algorithm>` with your chosen algorithm (e.g., `dilithium3`).

```bash
./rotate_keys.sh dilithium3
```

---

## **8. Troubleshooting Commands**

If you encounter issues, use the following commands to diagnose and resolve problems.

### **a. Check System Logs for Errors**

```bash
sudo journalctl -xe
```

---

## **9. Testing and Validation Commands**

Before deploying the generated keys in a production environment, test them to ensure they work correctly.

### **a. Test SSH Connection with the New Key**

Replace `<date>` with the actual date stamp used during key generation and `user@testserver` with your username and server address.

```bash
ssh -i ~/.ssh/id_dilithium3_<date> user@testserver
```

### **b. Verify Key Integrity**

Ensure that the key has been generated correctly.

```bash
ssh-keygen -lf ~/.ssh/id_dilithium3_<date>
```

---

## **Summary of Commands**

For your convenience, here's a consolidated list of all the commands mentioned above:

```bash
# 1. Verify OpenSSH and OpenSSL Versions
ssh -V
openssl version

# 2. Install Required Tools
sudo apt update
sudo apt install -y liboqs-dev openssl libssl-dev
sudo apt install -y oqs-tools  # Optional

# 3. Generate a Post-Quantum SSH Key Pair
oqs-keygen -a dilithium3 --security-level=3 -o ~/.ssh/id_dilithium3_$(date +%Y%m%d)

# 4. Secure Your Private Key
chmod 600 ~/.ssh/id_dilithium3_*
chmod 644 ~/.ssh/id_dilithium3_*.pub

# 5. Add the Key to Your SSH Agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_dilithium3_*
ssh-add -t 3600 ~/.ssh/id_dilithium3_*  # Optional

# 6. Share Your Public Key
cat ~/.ssh/id_dilithium3_*.pub

# 7. Automate Key Rotation
# a. Create the script
echo '#!/bin/bash

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

echo "Key rotation for $ALGO completed on $DATE."' > rotate_keys.sh

# b. Make the script executable
chmod +x rotate_keys.sh

# c. Run the script
./rotate_keys.sh dilithium3

# 8. Troubleshooting
sudo journalctl -xe

# 9. Testing and Validation
ssh -i ~/.ssh/id_dilithium3_<date> user@testserver
ssh-keygen -lf ~/.ssh/id_dilithium3_<date>
```
