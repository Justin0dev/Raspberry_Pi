# Installing `vcgencmd` on Raspberry Pi 5 (Kali OS)

This guide explains how to install and configure `vcgencmd` on a Raspberry Pi 5 running Kali Linux, which does not include `vcgencmd` by default.

## Option 1: Install Raspberry Pi Userland Tools

### Step 1: Install `libraspberrypi-bin`
1. Update the package list:
   ```bash
   sudo apt update
   ```
2. Install the Raspberry Pi userland tools:
   ```bash
   sudo apt install libraspberrypi-bin
   ```

### Step 2: Configure `/dev/vcio`
To ensure `vcgencmd` works correctly, create and set permissions for the `/dev/vcio` device:
```bash
sudo mknod /dev/vcio c 100 0
sudo chmod 666 /dev/vcio
```

### Step 3: Test `vcgencmd`
Verify the installation with the following commands:
```bash
vcgencmd version
vcgencmd measure_temp
vcgencmd get_throttled
vcgencmd measure_volts
vcgencmd measure_clock arm
vcgencmd get_config arm_freq
vcgencmd bootloader_config
```

## Option 2: Build and Install `raspberrypi-utils`

### Step 1: Install Prerequisites
Install the required dependencies:
```bash
sudo apt install cmake device-tree-compiler libfdt-dev
```

### Step 2: Build and Install `raspberrypi-utils`
1. Clone the repository:
   ```bash
   git clone https://github.com/raspberrypi/utils.git
   cd utils
   ```
2. Build and install:
   ```bash
   cmake .
   make
   sudo make install
   ```

### Step 3: Configure `/dev/vcio`
As in Option 1, set up the `/dev/vcio` device:
```bash
sudo mknod /dev/vcio c 100 0
sudo chmod 666 /dev/vcio
```

### Step 4: Test `vcgencmd`
Run the same test commands as in Option 1 to verify functionality.

## Make `/dev/vcio` Persistent Across Reboots
The `/dev/vcio` device is dynamically created at boot and does not persist. Use a udev rule to ensure proper permissions on reboot.

### Step 1: Create a udev Rule
1. Create a new udev rules file:
   ```bash
   sudo nano /etc/udev/rules.d/99-vcio.rules
   ```
2. Add the following line:
   ```
   KERNEL=="vcio", MODE="0666", GROUP="video"
   ```
3. Save and exit.

### Step 2: Reload udev Rules
Apply the udev rule:
```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

### Step 3: Verify Configuration
Check the permissions of `/dev/vcio`:
```bash
ls -l /dev/vcio
```
Expected output:
```
crw-rw-rw- 1 root video 100, 0 <timestamp> /dev/vcio
```