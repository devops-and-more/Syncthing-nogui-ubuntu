```markdown
# Syncthing Installation and Configuration Guide

This guide provides steps to install and configure Syncthing on Debian/Ubuntu-based systems. It includes instructions for setting up Syncthing to start on boot and synchronizing files between multiple devices.

## Installation

### Step 1: Update the System Package List
```bash
sudo apt update
```

### Step 2: Install Syncthing
```bash
sudo apt install syncthing -y
```

### Step 3: Start Syncthing Manually
Run Syncthing to generate the default configuration file:
```bash
syncthing
```
After a few seconds, stop Syncthing using `CTRL+C`.

---

## Configure Syncthing to Start on Boot

### Step 1: Create a Systemd Service File
Create the service file for Syncthing:
```bash
sudo tee /etc/systemd/system/syncthing@.service > /dev/null <<EOF
[Unit]
Description=Syncthing - Open Source Continuous File Synchronization
Documentation=https://docs.syncthing.net/
After=network.target

[Service]
User=%i
ExecStart=/usr/bin/syncthing serve --no-browser --home=/home/%i/.config/syncthing
Restart=on-failure
SuccessExitStatus=3 4

[Install]
WantedBy=default.target
EOF
```

### Step 2: Enable and Start Syncthing
Enable and start the Syncthing service for the current user:
```bash
sudo systemctl enable syncthing@$(whoami)
sudo systemctl start syncthing@$(whoami)
```

---

## Configuration

### Step 1: Locate the Configuration File
The default configuration file is located at:
```bash
~/.config/syncthing/config.xml
```

### Step 2: Update the Configuration File
Replace the content of `config.xml` with the following configuration:
```xml
<!-- the current machine has the id: OG36BYU-Y56Z4M7-ZDSAKYK-NWVB3VC-JHQEXAQ-GDXRF6T-2RAH62J-BYPTJA4
the second is OOUWTT2-FWLPYID-JMGCLDF-3PQPNAP-FXVDKJW-NNBYVTA-QOZMYPR-HP6E6QR
the third is IBREMM3-3TSQUBV-B6Q6PL3-NIAXWR6-DY7E6PK-624HM6S-QP4FP73-CKVL2Q2   
the folder that is being synced between the three is /home/vagrant/Sync-->
<configuration version="35">
    <!-- Include the provided configuration XML content here -->
</configuration>
```

### Step 3: Restart Syncthing
Restart the Syncthing service to apply the changes:
```bash
sudo systemctl restart syncthing@$(whoami)
```

---

## Verify the Setup

1. Check if Syncthing is running:
   ```bash
   systemctl status syncthing@$(whoami)
   ```

2. Test synchronization by adding or modifying files in `/home/vagrant/Sync` on one device. Ensure the changes are reflected in the `Sync` folder on the other devices.

---

## License

This guide is provided under the [MIT License](LICENSE).
```

You can now copy this `README.md` file into your GitHub repository for future reference.