Ubuntu GUI Desktop Environment for Ubuntu WSL2
This guide provides instructions to set up and configure a graphical desktop environment (GUI) for Ubuntu running on Windows Subsystem for Linux 2 (WSL2). The process involves enabling systemd, installing a minimal or full Ubuntu desktop, and configuring X11 forwarding to view the desktop environment in WSL2 using a tool like X410. Follow the steps carefully to create a fully functional Ubuntu desktop experience within WSL2.

Prerequisites
WSL2 with Ubuntu installed
X410 (for displaying the GUI)
Administrative privileges on the Windows machine
Setup Instructions
Step 1: Enable Systemd in WSL2
Open Ubuntu Terminal
Launch the terminal and execute the following command to create and edit the WSL configuration file:

bash
Copy code
sudo nano /etc/wsl.conf
Edit the Config File
Once the file is opened, enter the following lines and save the file:

ini
Copy code
[boot]
systemd=true
Restart WSL
Disable WSL2 GUI and restart the WSL2 environment to apply the changes:

bash
Copy code
wsl --shutdown
Step 2: Create Default Directories
Run the following command to create commonly used folders like Desktop, Documents, etc.:

bash
Copy code
xdg-user-dirs-update
Step 3: Change Default Login Mode
Change the default login mode to multi-user.target to enable better system control:

bash
Copy code
sudo systemctl set-default multi-user.target
Step 4: Install ACPI and Disable It
Install the acpid package, which monitors ACPI events such as power button presses:

bash
Copy code
sudo apt install acpid -y
Disable the acpid service to prevent it from running:

bash
Copy code
sudo systemctl disable --now acpid.service acpid.socket acpid.path
Step 5: Install Firefox
To install Firefox, use the following command:

bash
Copy code
sudo apt install firefox
If an error occurs, such as a blocked process, execute the following steps to resolve it:

Kill the process causing the error:

bash
Copy code
sudo kill <process-id>
Run the following commands to reinstall snapd:

bash
Copy code
sudo dpkg --configure -a
sudo apt update
sudo apt install --reinstall snapd
Install the necessary packages:

bash
Copy code
sudo apt-get update && sudo apt-get install -yqq daemonize dbus-user-session fontconfig
Start and enable snapd:

bash
Copy code
sudo daemonize /usr/bin/unshare --fork --pid --mount-proc /lib/systemd/systemd --system-unit=basic.target
exec sudo nsenter -t $(pidof systemd) -a su - $LOGNAME
sudo systemctl start snapd
sudo systemctl enable snapd
Check the snapd version:

bash
Copy code
snap version
Step 6: Install Ubuntu Desktop Environment
You can choose between a minimal environment or a full Ubuntu desktop environment with additional applications and tools.

Minimal Desktop:

bash
Copy code
sudo apt install ubuntu-desktop-minimal -y
Full Ubuntu Desktop:

bash
Copy code
sudo apt install ubuntu-desktop -y
Step 7: Configure Environment Variables
Add the following lines to your .bashrc file to configure the desktop environment:

bash
Copy code
export XDG_CURRENT_DESKTOP=ubuntu:GNOME
export XDG_SESSION_DESKTOP=ubuntu
export DESKTOP_SESSION=ubuntu
export GNOME_SHELL_SESSION_MODE=ubuntu
export XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
export XDG_DATA_DIRS=/usr/share/ubuntu:/usr/local/share:/usr/share:/var/lib/snapd/desktop
export XDG_MENU_PREFIX=gnome-
export XDG_SESSION_TYPE=x11
export XDG_SESSION_CLASS=user
export GDK_BACKEND=x11
export LIBGL_ALWAYS_SOFTWARE=1
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2; exit;}'):0.0
If you have custom tools in your environment, also add this:

bash
Copy code
export PATH="$HOME/os/toolchain/x86_64/bin:$PATH"
To start the GNOME session, run:

bash
Copy code
gnome-session --session=ubuntu
Step 8: Install and Configure X410
Download and install X410 from the following website:
https://x410.dev

Once installed, open X410 and select "Floating Windows Mode."

Open your Ubuntu WSL2 terminal, and the desktop environment should launch automatically.

Step 9: Fix Missing Environment Variables for Ubuntu Command
If the ubuntu command is not launching Ubuntu inside the Windows Command Prompt, add the following directory to your system's environment variables:

plaintext
Copy code
%userprofile%\AppData\Local\Microsoft\WindowsApps
Step 10: Disable WSL2 GUI (Optional)
To stop applications from opening in the WSL2 GUI, create a .wslconfig file in your Windows user folder (%userprofile%), and add the following:

ini
Copy code
[wsl2]
guiApplications=false
Restart WSL2 with:

bash
Copy code
wsl --shutdown
Step 11: Check the Status of WSL Distros
To check the status of your WSL distributions, use the following commands:

bash
Copy code
wsl --list --verbose
or

bash
Copy code
wsl -l -v
Step 12: Use Mirrored Network Mode
If you prefer to mirror the network instead of using bridge mode, add the following to your .wslconfig file in your Windows user folder:

ini
Copy code
[wsl2]
networkingMode=mirrored
Save the file and restart WSL with:

bash
Copy code
wsl --shutdown
Additional Information
For more details on enabling systemd and configuring X410, refer to the official guide here:
Enable Systemd in WSL2 and have the best Ubuntu GUI Desktop Experience
