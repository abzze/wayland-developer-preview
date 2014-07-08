Setting up an evaluation environment
====================================

This document explains how to set up a virtual machine running Ubuntu 14.04 Server for the Developer Preview.

1. Run the following commands in order to set up the environment:

   ```
   export WLD=/usr/local
   export LD_LIBRARY_PATH=$WLD/lib
   sudo apt-get install build-essential git autoconf automake libtool pkg-config libffi-dev libexpat1-dev libxkbcommon-dev libegl1-mesa-dev libgles2-mesa-dev libxcb-composite0-dev libxcursor-dev libpixman-1-dev libcairo2-dev libudev-dev libgbm-dev libmtdev-dev libjpeg-dev libpam0g-dev
   ```

2. Run the following commands to build Wayland:

   ```
   git clone git://anongit.freedesktop.org/wayland/wayland
   cd wayland
   ./autogen.sh --prefix=$WLD --disable-documentation
   make
   sudo make install
   cd ..
   ```

3. Run the following commands to build Weston:

   ```
   git clone git://anongit.freedesktop.org/wayland/weston
   cd weston
   ./autogen.sh --prefix=$WLD --enable-screen-sharing
   sudo make install
   cd ..
   ```

4. Run the following commands to add your user to groups:

   ```
   sudo addgroup --system weston-launch
   sudo adduser user weston-launch
   sudo adduser user video
   ```

5. Run the following commands to create a default initialization file:

   ```
   mkdir ~/.config
   cp weston/weston.ini ~/.config
   ``` 

6. Edit the ``~/.config/weston.ini`` default initialization file as follows:

   ```
   # ~/.config/weston.ini
   
   [core]
   modules=screen-share.so
   [screen-share]
   command=vncserver-wayland
   ```

7. Run the following commands to install and license VNC Server 5.2.0:

   ```
   sudo dpkg -i <VNC-Server>.deb
   sudo apt-get -f install   # If the above command reports a dependency failure
   sudo vnclicense -add <key>.vnclicense
   ```

8. Run the following commands to prepare the Developer Preview:

   ```
   tar xvf <Developer-Preview>.tar.gz
   cd VNC-Server-Wayland-<version>
   sudo cp vncserver* /usr/local/bin
   ```

Configuring VNC Server in User Mode only
========================================

**Note:** This step is only necessary for a VirtualBox virtual machine.

1. Edit ``/etc/default/grub`` as follows:

   ```
   # /etc/default/grub
   
   GRUB_GFXMODE=1024x768
   GRUB_GFXPAYLOAD_LINUX=keep
   ```
   
2. Run the command ``sudo grub-mkconfig -o /boot/grub/grub.cfg`` to rebuild GRUB.

3. Reboot the virtual machine.

Starting VNC Server in User Mode only
=====================================

**Note:** This step is only necessary for a VirtualBox virtual machine.

1. Run the command ``weston-launch -- --backend=fbdev-backend.so``.

2. Press Ctrl+Alt+S.
