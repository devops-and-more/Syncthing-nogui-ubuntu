# Syncthing Setup for Remote Server Synchronization

This README provides step-by-step instructions for setting up **Syncthing** to sync files between a local machine and a remote server, such as an EC2 instance.

## Prerequisites

- Syncthing installed on both local and remote machines.
- SSH key for authentication with the remote server.
- **Remote Server:** In this example, we will use an EC2 instance (replace with your own server details).
- **Local Server:** The machine you want to synchronize with the remote server.

---

## 1. SSH Key Setup

Ensure that you have the SSH key on your local machine, which is used to authenticate with your remote EC2 instance.

---

## 2. Open SSH Tunnel (if required)

If your local machine and the remote server are **not** on the same network, you will need to create an SSH tunnel to allow communication between the local machine and the EC2 instance.

Run the following command to create the tunnel:

```bash
ssh -i ~/.ssh/virginia.pem \
   -L 127.0.0.1:22001:127.0.0.1:22000 \
   -R 127.0.0.1:22001:127.0.0.1:22000 \
   ubuntu@ec2-98-81-137-73.compute-1.amazonaws.com
```

### Explanation of SSH Tunnel Options:

- `-L 127.0.0.1:22001:127.0.0.1:22000`: Forward local port `22001` to the remote machine's port `22000`.
- `-R 127.0.0.1:22001:127.0.0.1:22000`: Reverse the port forwarding from the remote machine back to the local machine.
- `ubuntu@ec2-98-81-137-73.compute-1.amazonaws.com`: SSH login details for the EC2 instance.

If your local machine and the remote server are **on the same network**, **skip this SSH tunnel step** and move directly to modifying the Syncthing configuration files.

---

## 3. Modify Syncthing Configuration Files

After setting up the SSH tunnel (if necessary), you need to modify the **Syncthing** config files on both your local machine and the remote EC2 instance.

### Local Server Configuration:

On your **local server**, open the Syncthing config file located at `~/.config/syncthing/config.xml`.

Ensure that the configuration includes the necessary **remote devices** and their synchronization paths. Here is an example configuration with comments:

```xml
<!-- the current machine has the id: OG36BYU-Y56Z4M7-ZDSAKYK-NWVB3VC-JHQEXAQ-GDXRF6T-2RAH62J-BYPTJA4
the second is OOUWTT2-FWLPYID-JMGCLDF-3PQPNAP-FXVDKJW-NNBYVTA-QOZMYPR-HP6E6QR
the third is IBREMM3-3TSQUBV-B6Q6PL3-NIAXWR6-DY7E6PK-624HM6S-QP4FP73-CKVL2Q2   
the folder that is being synced between the three is /home/vagrant/Sync-->

<configuration version="35">
    <folder id="default" label="Default Folder" path="/home/vagrant/Sync" type="sendreceive" rescanIntervalS="3600" fsWatcherEnabled="true" fsWatcherDelayS="10" ignorePerms="false" autoNormalize="true">
        <filesystemType>basic</filesystemType>
        <device id="OG36BYU-Y56Z4M7-ZDSAKYK-NWVB3VC-JHQEXAQ-GDXRF6T-2RAH62J-BYPTJA4" introducedBy="">
            <encryptionPassword></encryptionPassword>
        </device>
        <device id="OOUWTT2-FWLPYID-JMGCLDF-3PQPNAP-FXVDKJW-NNBYVTA-QOZMYPR-HP6E6QR" introducedBy="">
            <encryptionPassword></encryptionPassword>
        </device>
        <device id="IBREMM3-3TSQUBV-B6Q6PL3-NIAXWR6-DY7E6PK-624HM6S-QP4FP73-CKVL2Q2" introducedBy="">
            <encryptionPassword></encryptionPassword>
        </device>
        <!-- Other folder and device configurations follow... -->
    </folder>
    <!-- Devices and other configuration... -->
</configuration>
```

### Remote EC2 Server Configuration:

On the **remote EC2 instance**, access the Syncthing configuration file at `~/.config/syncthing/config.xml` and add similar configurations, ensuring that the remote device's ID and synchronization paths are set up correctly.

For instance, you might want to add a folder for syncing files from the remote EC2 instance to the local machine.

---

## 4. Restart Syncthing

After modifying the configuration files on both the local and remote servers, restart Syncthing on both machines to apply the changes.

On the **local machine**:

```bash
syncthing -restart
```

On the **remote EC2 instance**:

```bash
syncthing -restart
```

---

## 5. Verify Synchronization

After the restart, check the Syncthing GUI (or use `syncthing` command-line output) to ensure that the synchronization between the local and remote servers is functioning properly.

---

## Additional Considerations

- **SSH Tunnel Persistence:** You may want to ensure the SSH tunnel persists across reboots. You can use tools like `autossh` to automatically restart the tunnel if it drops.
- **Firewall and Security Groups:** Make sure that the necessary ports (e.g., `22000` for Syncthing) are open in your EC2 instance's security group settings.
- **Troubleshooting:** If synchronization fails, check the logs (`~/.config/syncthing/` or Syncthing UI) for more details.

---

Now you can synchronize files between your local server and a remote EC2 instance over an SSH tunnel (if needed)!

```

