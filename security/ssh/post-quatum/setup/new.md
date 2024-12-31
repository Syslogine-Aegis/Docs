## Quantum-Safe SSH Setup Guide

Welcome to our guide on setting up quantum-safe SSH for secure communication. This guide is tailored for both client and server-side configurations, ensuring robust quantum-safe encryption.

### Client Side Setup (Ubuntu)

#### Step 1: Install Required Packages
To get started, install the necessary tools and libraries:
```bash
sudo apt update
sudo apt install build-essential cmake ninja-build automake autoconf git python3 pkg-config python3-pytest libtool zlib1g-dev

# Clone and build liboqs
git clone https://github.com/open-quantum-safe/liboqs.git
cd liboqs
mkdir build && cd build
cmake -GNinja -DBUILD_SHARED_LIBS=ON ..
ninja
sudo ninja install
sudo ldconfig
```

#### Step 2: Build Quantum-Safe OpenSSH
Next, build a quantum-safe version of OpenSSH:
```bash
git clone https://github.com/open-quantum-safe/openssh.git
cd openssh
autoreconf
./configure --with-libs=-loqs --with-ldflags=-L/usr/local/lib
make -j
sudo make install
```

### Verification

To ensure the dynamic library is properly installed and recognized:
1. Check if the library exists:
   ```bash
   ls /usr/local/lib/liboqs.so*
   ```
2. Verify it is included in the dynamic linker cache:
   ```bash
   ldconfig -p | grep oqs
   ```

If the dynamic library is missing or not recognized, follow these steps to rebuild it:

### Rebuild liboqs with Dynamic Libraries

1. Navigate to the build directory of `liboqs`:
   ```bash
   cd ~/liboqs/build  # Adjust this to the correct location of your liboqs build directory
   ```
   If the directory no longer exists, clone and rebuild `liboqs`:
   ```bash
   git clone https://github.com/open-quantum-safe/liboqs.git
   cd liboqs
   mkdir build && cd build
   ```

2. Reconfigure the build to enable dynamic libraries:
   ```bash
   cmake -GNinja -DOQS_BUILD_ONLY_LIB=OFF -DBUILD_SHARED_LIBS=ON ..
   ```

3. Build and install the library:
   ```bash
   ninja
   sudo ninja install
   ```

4. Update the dynamic library cache:
   ```bash
   sudo ldconfig
   ```

### Note
Ensuring dynamic libraries are properly installed is critical for runtime compatibility and linking. If you encounter issues, verify the steps above or contact support for assistance.

#### Step 2: Build Quantum-Safe OpenSSH
Next, build a quantum-safe version of OpenSSH:
```bash
git clone https://github.com/open-quantum-safe/openssh.git
cd openssh
autoreconf
./configure --with-libs=-loqs --with-ldflags=-L/usr/local/lib
make -j
sudo make install
```

#### Step 3: Generate Your Quantum-Safe Key
Generate a secure quantum-safe key for authentication:
```bash
oqs-keygen -a dilithium3 --security-level=3 -o ~/.ssh/id_dilithium3_$(date +%Y%m%d)
```

---

### Server Side Setup (Debian)

#### Step 1: Install liboqs (Same as Client)
Follow the same steps as the client to install liboqs:
```bash
sudo apt update
sudo apt install build-essential cmake ninja-build automake autoconf git python3 pkg-config python3-pytest

git clone https://github.com/open-quantum-safe/liboqs.git
cd liboqs
mkdir build && cd build
cmake -GNinja ..
ninja
sudo ninja install
sudo ldconfig
```

#### Step 2: Build and Install Quantum-Safe OpenSSH
Build and install the server-side version of quantum-safe OpenSSH:
```bash
git clone https://github.com/open-quantum-safe/openssh.git
cd openssh
autoreconf
./configure --with-libs=-loqs --with-ldflags=-L/usr/local/lib
make -j
sudo make install
```

#### Step 3: Update SSH Configuration
Edit the SSH configuration file to accept quantum-safe keys:
```bash
sudo nano /etc/ssh/sshd_config
```
Add the following line:
```
PubkeyAcceptedKeyTypes +ssh-dilithium3
```

#### Step 4: Restart SSH Service
Restart the SSH service to apply changes:
```bash
sudo systemctl restart sshd
```

#### Step 5: Add Public Key
Add the quantum-safe public key to the server:
```bash
mkdir -p ~/.ssh
cat ~/id_dilithium3_YYYYMMDD.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

---

### Client SSH Configuration
To use the quantum-safe key for SSH, configure your client:
```bash
nano ~/.ssh/config
```
Add the following lines:
```
Host your-server-hostname
    IdentityFile ~/.ssh/id_dilithium3_YYYYMMDD
    PubkeyAcceptedKeyTypes +ssh-dilithium3
```

---

### Important Notes
- Always test in a staging environment before deploying to production.
- Maintain a backup SSH access to avoid lockouts.
- Regularly back up your quantum-safe keys.
- Document your setup for future reference and maintenance.

Thank you for securing your communication with quantum-safe technology! If you have any questions or need assistance, feel free to contact our support team.