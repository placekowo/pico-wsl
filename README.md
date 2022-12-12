# Not finished yet, too tired.
# Setting up the Pico SDK, including Picoprobe debugging, on Windows 10/11 via WSL2.

intro stuff here

## Prerequisites
Ensure that your Windows install is fully updated before starting.

This will only work for the x64 version of Windows 10, as there is no WSL support in the x86 version.

For Windows 10, version 1903, build 18632 or later is required. Any version of Windows 11 will work.

For debugging, I assume that you have a Picoprobe already set up. If not, refer to the [official documentation](https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf). 
Things will differ between this guide and the documentation, as the documentation assumes you are running on a Raspberry Pi. However, once the SDK is setup, 
you will be able to build Picoprobe. You do not need to install the Windows drivers using Zadig, as we will be passing through the Picoprobe to WSL later.

## Setup WSL
I will be installing Debian, however the official method of using ```wsl --install``` will result in an old Debian release on Windows 10. If you're using Windows 11,
you can skip to setting up the Pico SDK, as ```wsl --install -d Debian``` will automatically setup WSL2, install the latest release of Debian, and update the Linux kernel.

In an administrator Powershell window:

### Enable WSL 1
```dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart```

### Enable Virtual Machine features
```dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart```

Restart your system to complete the WSL install.

### Install the WSL2 Linux kernel update

Download and run [wsl_update_x64.msi](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi).

### Set WSL2 as the default WSL version

```wsl --set-default-version 2```

### Update the WSL2 Linux kernel to the latest version

```wsl --update```

WSL2 installation is complete. You may install any Linux distro available in the Microsoft store, however I will be using Debian. Optionally, you may wish
to install the [Windows Terminal](https://apps.microsoft.com/store/detail/windows-terminal/9N0DX20HK701).

## Install Debian
As mentioned earlier, this has to be installed manually on Windows 10, as the ```wsl --install``` command will install an old release. This can be installed
through the Microsoft Store, or manually, by installing [this](https://aka.ms/wsl-debian-gnulinux) AppX package.

After installation, you will be prompted to set up a user account. Once this is done:

## Setup the Pico SDK

I am putting everything Pico related into the ```~/pico``` directory.

### Update packages and repositories

```sudo apt update && sudo apt -y upgrade```

### Installing the SDK

```
sudo apt install -y git cmake gcc-arm-none-eabi libnewlib-arm-none-eabi build-essential python3
cd ~
mkdir pico
cd pico
git clone -b master --recursive https://github.com/raspberrypi/pico-sdk.git
echo 'export PICO_SDK_PATH=~/pico/pico-sdk' >> ~/.bashrc
source ~/.bashrc
```
### Testing the SDK
Let's build the ```blink``` example from the [pico-examples](https://github.com/raspberrypi/pico-examples) repo to test that everything is working.
```cd ~/pico
git clone -b master https://github.com/raspberrypi/pico-examples.git
cd pico-examples
mkdir build
cd build
cmake ..
cd blink
make -j4
```
To make life easier, ```explorer.exe .``` will open our build directory in Windows. Here we will find our .uf2 file for flashing.

If you just want to build Pico programs, then we can stop here. However, I also would like to use VSCode as my IDE, along with debugging with a Picoprobe.

## Set up VS Code

### Install VSCode

Install VSCode from [here](https://code.visualstudio.com/download), ensuring that we select the option to add VSCode to PATH.

In a Powershell window:

```code --install-extension ms-vscode-remote.vscode-remote-extensionpack```

Then, in Debian:

```code```

VSCode will automatically set itself up for remote development in WSL. Once this is done, exit VSCode, and in Debian:

```
code --install-extension ms-vscode.cmake-tools
code --install-extension ms-vscode.cpptools
```

We can now use VSCode to develop and build programs via WSL. Let's setup debugging now.


