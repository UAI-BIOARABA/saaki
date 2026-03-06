# G1 Interface

The provided tutorial will assist you with setting up and operating your G1 bi-pedal robot.
The tutorial topics are listed in the left column and presented in the suggested reading order.

## G1 Quick Start

1. **Power on the robot**.
2. **Connect LAN cable** to the Ethernet port (the top-most available port) located on the back side of the neck.

## Static Network Connection

For first-time setup, you need to connect via LAN cable to configure the robot's network.

**On your PC (Ubuntu):**

1. Navigate to **Settings → Network**.
2. Click on the "+" icon to create a new connection.
3. In **IPv4 Settings**, set the connection method to **Manual**.
4. Enter the following details:
   - **Address IP**: `192.168.123.51`
   - **Netmask**: `24`
5. Save and restart your network.

After a successful connection, check the host PC's local IP:

```bash
ifconfig
```

Ping the robot to verify connectivity:

```bash
ping 192.168.123.164
```

Access the robot via SSH:

```bash
ssh -X unitree@192.168.123.164
```

Default password:

```bash
123
```

## Network Table

| IP Address           | Device            | Username | Password  |
| -------------------- | ----------------- | -------- | --------- |
| 192.168.123.161      | G1 MCU            | x        | x         |
| 192.168.123.164      | G1 Auxiliary PC 2 | unitree  | 123       |
| 192.168.123.120      | Mid360 Lidar      | x        | x         |

> **Note**
> Do **not** set your PC IP as any of the G1 IP addresses listed below.

## Enable Internet LAN

This enables LAN via the ethernet cable. Ensure the cable is connected to a router with internet access.

```bash
sudo ip link set eth1 down && sudo ip link set eth1 up
sudo dhclient eth1
sudo date -s "$(wget --method=HEAD -qSO- --max-redirect=0 google.com 2>&1 | sed -n 's/^ *Date: *//p')"
ping google.com
```

## Enable Internet WiFi (Recommended)

You can attach a hub and WiFi via a hub. A link to a cost-effective hub and WiFi stick is provided in Enhancements.

```bash
sudo ip link set eth0 down && sudo ip link set eth0 up
sudo dhclient eth0
```