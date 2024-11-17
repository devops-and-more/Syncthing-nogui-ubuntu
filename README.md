# Syncthing Setup and Configuration

This guide provides step-by-step instructions to install, configure, and set up Syncthing on Debian/Ubuntu-based systems. It also includes a method to automate the creation of the systemd service file.

---

## Installation Steps

### 1. Update the System and Install Syncthing
Run the following commands to update your system and install Syncthing:

```bash
sudo apt-get update
sudo apt-get install -y curl apt-transport-https gnupg
curl -s https://syncthing.net/release-key.txt | sudo apt-key add -
echo "deb https://apt.syncthing.net/ syncthing release" | sudo tee /etc/apt/sources.list.d/syncthing.list
sudo apt-get update
sudo apt-get install -y syncthing
```

---

### 2. Configure Syncthing Systemd Service
Use the provided script to generate the `syncthing@.service` file with user-specific variables. This ensures Syncthing starts automatically with the system.

#### Script: `setup-syncthing-service.sh`
```bash
#!/bin/bash

# Define variables
USERNAME="vagrant"  # Replace with your desired username
SYNCTHING_HOME="/home/$USERNAME/.config/syncthing"

# Generate the systemd service file
sudo tee /etc/systemd/system/syncthing@.service > /dev/null <<EOF
[Unit]
Description=Syncthing - Open Source Continuous File Synchronization
Documentation=https://docs.syncthing.net/
After=network.target

[Service]
User=$USERNAME
ExecStart=/usr/bin/syncthing serve --no-browser --home=$SYNCTHING_HOME
Restart=on-failure
SuccessExitStatus=3 4

[Install]
WantedBy=default.target
EOF

# Inform the user
echo "Syncthing systemd service file has been created for user: $USERNAME"
```

#### Steps to Run the Script:
1. Save the script as `setup-syncthing-service.sh`.
2. Make the script executable:
   ```bash
   chmod +x setup-syncthing-service.sh
   ```
3. Run the script:
   ```bash
   ./setup-syncthing-service.sh
   ```

---

### 3. Enable and Start Syncthing Service
After creating the systemd service file, enable and start Syncthing for the desired user.

```bash
sudo systemctl enable syncthing@vagrant.service
sudo systemctl start syncthing@vagrant.service
```

Replace `vagrant` with the username you specified in the script.

---

## Configuring Syncthing Manually (Optional)

### Locate and Edit the Configuration File
The Syncthing configuration file is located at:
```
/home/<username>/.config/syncthing/config.xml
```

#### Example Configuration
The following is an example configuration file with comments explaining each part:

```xml
<!-- the current machine has the id: OG36BYU-Y56Z4M7-ZDSAKYK-NWVB3VC-JHQEXAQ-GDXRF6T-2RAH62J-BYPTJA4
the second is OOUWTT2-FWLPYID-JMGCLDF-3PQPNAP-FXVDKJW-NNBYVTA-QOZMYPR-HP6E6QR
the third is IBREMM3-3TSQUBV-B6Q6PL3-NIAXWR6-DY7E6PK-624HM6S-QP4FP73-CKVL2Q2
the folder that is been synced between the three is /home/vagrant/Sync -->
<configuration version="35">
<!-- see the example in the files -->
</configuration>
```

---

## Testing the Setup

1. Verify Syncthing is running:
   ```bash
   sudo systemctl status syncthing@vagrant.service
   ```
2. Confirm synchronization between devices by adding them to the Syncthing network.

---

## Uninstall Syncthing

If you want to remove Syncthing and its associated files:

1. Stop and disable the service:
   ```bash
   sudo systemctl stop syncthing@vagrant.service
   sudo systemctl disable syncthing@vagrant.service
   ```
2. Remove the Syncthing package:
   ```bash
   sudo apt-get purge syncthing
   sudo apt-get autoremove
   ```
3. Delete configuration files:
   ```bash
   rm -rf /home/vagrant/.config/syncthing
   sudo rm /etc/systemd/system/syncthing@.service
   ```

---

## Additional Notes

- Replace `vagrant` with your specific username throughout this guide.
- Ensure the `config.xml` file matches your device IDs and paths for successful synchronization.