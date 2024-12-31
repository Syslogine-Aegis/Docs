## Quantum-Safe SSH Setup Guide

Welcome to our comprehensive guide on setting up quantum-safe SSH for secure communication. This guide is tailored for both client and server-side configurations, ensuring robust quantum-safe encryption.

### Client Side Setup (Ubuntu)

#### Step 1: Install Required Packages
To get started, install the necessary tools and libraries:
```bash
sudo apt update
sudo apt install build-essential cmake ninja-build automake autoconf git python3 pkg-config python3-pytest libtool zlib1g-dev

git clone https://github.com/open-quantum-safe/liboqs.git
cd liboqs
mkdir build && cd build
cmake -GNinja ..
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

---



De aanwezigheid van `/usr/local/lib/liboqs.a` geeft aan dat de statische versie van de `liboqs`-bibliotheek correct is geïnstalleerd, maar de dynamische versie (`liboqs.so`) ontbreekt. Dynamische bibliotheken zijn nodig voor runtime-koppeling en worden opgenomen in de dynamische bibliotheekcache, terwijl statische bibliotheken alleen worden gebruikt tijdens het bouwen van applicaties.

Je moet de dynamische bibliotheek (`liboqs.so`) bouwen en installeren. Hier is hoe je dat doet:

---

### **1. Reconfigureer en bouw liboqs met dynamische bibliotheken**
1. Ga naar de build-directory van `liboqs`:
   ```bash
   cd ~/liboqs/build  # Pas dit aan naar de juiste locatie van je liboqs-build
   ```
   Als de directory niet meer bestaat, herhaal het klonen en bouwen van `liboqs`:
   ```bash
   git clone https://github.com/open-quantum-safe/liboqs.git
   cd liboqs
   mkdir build && cd build
   ```

2. Reconfigureer de build om dynamische bibliotheken in te schakelen:
   ```bash
   cmake -GNinja -DBUILD_SHARED_LIBS=ON ..
   ```

3. Bouw en installeer:
   ```bash
   ninja
   sudo ninja install
   ```

4. Update de dynamische bibliotheekcache:
   ```bash
   sudo ldconfig
   ```

---

### **2. Controleer de installatie**
Controleer opnieuw of de dynamische bibliotheek aanwezig is:
```bash
ls /usr/local/lib/liboqs.so*
```

Controleer of de bibliotheek in de cache is geladen:
```bash
ldconfig -p | grep oqs
```

---

### **3. Opmerking over statische bibliotheken**
De statische bibliotheek (`liboqs.a`) is bruikbaar als je applicaties bouwt die expliciet de bibliotheek linken, maar dynamische bibliotheken (`liboqs.so`) zijn nodig voor runtime-koppeling en dynamisch laden.

---

Na het uitvoeren van bovenstaande stappen zou alles correct moeten werken. Laat weten als je verdere hulp nodig hebt! 😊