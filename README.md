# rpi-hotspot
## Wi-Fi Hotspot Configuration Script for Raspberry Pi

This project contains a Bash script that configures a Wi-Fi hotspot on a Raspberry Pi using NetworkManager. The script automatically sets up a secure Wi-Fi network with a defined DHCP range and configures NAT routing to allow devices connected to the hotspot to access the internet.

### Features

- **Automatic Wi-Fi Hotspot Configuration**: The script sets up a secure Wi-Fi network with a user-defined SSID and password.
- **Customizable DHCP Range**: Users can define the range of IP addresses assigned to clients via DHCP.
- **NAT Routing**: The script configures NAT to allow devices connected to the hotspot to access the internet.
- **Default Values**: Default values are used if no input is provided by the user.

### Usage

1. **Download the script**:
    ```bash
    wget https://github.com/your-username/your-repo/raw/main/setup_hotspot.sh
    chmod +x setup_hotspot.sh
    ```

2. **Run the script**:
    ```bash
    sudo ./setup_hotspot.sh
    ```

3. **Enter the required information** or simply press Enter to use the default values.

### Default Values

- **SSID**: RpiHotSpot
- **Password**: password
- **Hotspot IP Address**: 192.168.50.1
- **DHCP Range Start**: 192.168.50.2
- **DHCP Range End**: 192.168.50.10
- **DHCP Netmask**: 255.255.255.0

### Uninstallation Script

To revert to the initial state and remove the configurations created by the setup script, you can use the following uninstallation script:

```bash
#!/bin/bash

# Function to restore a backup file if it exists
restore_file() {
    local original_file=$1
    local backup_file="${original_file}.backup"

    if [ -f "$backup_file" ]; then
        sudo mv "$backup_file" "$original_file"
        echo "Restored: $original_file"
    else
        echo "No backup found for: $original_file"
    fi
}

# Deactivate and delete the hotspot Wi-Fi connection
if nmcli connection show | grep -q "MyHotspot"; then
    nmcli connection down MyHotspot
    nmcli connection delete MyHotspot
    echo "Deactivated and deleted MyHotspot Wi-Fi connection."
else
    echo "No MyHotspot connection found."
fi

# Restore NetworkManager configuration
restore_file "/etc/NetworkManager/NetworkManager.conf"

# Delete the hotspot configuration file
if [ -f "/etc/NetworkManager/system-connections/MyHotspot.nmconnection" ]; then
    sudo rm /etc/NetworkManager/system-connections/MyHotspot.nmconnection
    echo "Deleted: /etc/NetworkManager/system-connections/MyHotspot.nmconnection"
else
    echo "No MyHotspot.nmconnection file found."
fi

# Restore sysctl.conf file
restore_file "/etc/sysctl.conf"
sudo sysctl -p

# Flush iptables rules and delete saved rules
if [ -f "/etc/iptables/rules.v4" ]; then
    sudo iptables -t nat -F
    sudo rm /etc/iptables/rules.v4
    echo "Restored default iptables rules."
else
    echo "No specific iptables rules found."
fi

# Disable and remove the iptables-restore service
if [ -f "/etc/systemd/system/iptables-restore.service" ]; then
    sudo systemctl disable iptables-restore.service
    sudo rm /etc/systemd/system/iptables-restore.service
    sudo systemctl daemon-reload
    echo "Removed and disabled iptables-restore service."
else
    echo "No iptables-restore service found."
fi

echo "System reset complete. The system should be back to its initial state."
```

### Using the Uninstallation Script

1. **Download the uninstallation script**:
    ```bash
    wget https://github.com/your-username/your-repo/raw/main/uninstall_hotspot.sh
    chmod +x uninstall_hotspot.sh
    ```

2. **Run the uninstallation script**:
    ```bash
    sudo ./uninstall_hotspot.sh
    ```

This script will reset your system to its initial state, except for the installed packages, which will remain.

### Notes

- **Administrative privileges**: Both scripts require administrative privileges to make system changes.
- **Tested on Raspberry Pi**: These scripts are specifically designed and tested to work on a Raspberry Pi using NetworkManager.

---

Feel free to adjust the URLs to match your GitHub repository and customize the text as needed.
