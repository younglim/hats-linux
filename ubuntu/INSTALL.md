# hats for Linux (Ubuntu 18.04 LTS or later)

Steps to install test automation tools and dependencies for Ubuntu.

## Pre-requisites
- tested on Ubuntu Desktop
- Run commands as a sudoers user
- Install the following minimum packages:

```
sudo apt install unzip

```

## Instructions

```
# Remove Password sudo
sudo echo "$USER ALL=(ALL:ALL) NOPASSWD: ALL" | sudo tee --append /etc/sudoers.d/$USER

# Install Oracle JDK 8
sudo add-apt-repository ppa:webupd8team/java -y
sudo apt update -y
echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 select true" | sudo debconf-set-selections
sudo apt install oracle-java8-set-default -y

# Install Android SDK with SDK Manager
wget https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip
sudo mkdir /opt/android-sdk
sudo chown $USER /opt/android-sdk
unzip sdk-tools-linux-4333796.zip -d /opt/android-sdk
echo "export ANDROID_SDK_ROOT=/opt/android-sdk" >> ~/.bash_profile
echo "export ANDROID_HOME=/opt/android-sdk" >> ~/.bash_profile
echo "export ANDROID_SDK_HOME=~" >> ~/.bash_profile
echo "export PATH=\$ANDROID_HOME/platform-tools:\$PATH" >> ~/.bash_profile
echo "export PATH=\$ANDROID_HOME/tools:\$PATH" >> ~/.bash_profile
echo "export PATH=\$ANDROID_HOME/tools/bin:\$PATH" >> ~/.bash_profile
echo "export PATH=\$ANDROID_SDK_ROOT/emulator:\$PATH" >> ~/.bash_profile
source ~/.bash_profile
yes | sdkmanager --licenses

# Install Build Tools
sdkmanager 'build-tools;28.0.2' 'platform-tools' 'platforms;android-28'

# Install ant maven gradle
sudo apt install ant maven gradle -y

# Install Xvfb
sudo apt install xvfb -y
sudo wget https://raw.githubusercontent.com/younglim/hats-linux/master/scripts/xvfb -P /opt/
sudo chmod +x /opt/xvfb
echo "sudo /opt/xvfb start" >> ~/.bash_profile
echo "export DISPLAY=:99" >> ~/.bash_profile
. ~/.bash_profile

# Install Python pip and virtualenv
sudo apt install python-pip -y
sudo apt install virtualenv -y

# Create hats virtualenv
sudo mkdir /opt/hats
sudo chown $USER /opt/hats
virtualenv /opt/hats
source /opt/hats/bin/activate
echo "source /opt/hats/bin/activate" >> ~/.bash_profile

# Install hats virtualenv
easy_install functools
pip install python-xlib
sudo apt install libjpeg-dev zlib1g-dev -y
pip install -r https://raw.githubusercontent.com/younglim/hats-ci/master/install-list/pip-install-list.txt

# Install npm and appium
sudo apt install npm -y
sudo npm install -g appium@1.8.1

# Install usbutils
sudo apt install usbutils -y

# Edit udev to allow usb devices for non-root users
wget https://raw.githubusercontent.com/M0Rf30/android-udev-rules/master/51-android.rules
sudo cp 51-android.rules /etc/udev/rules.d/51-android.rules
sudo chmod a+r /etc/udev/rules.d/51-android.rules
sudo groupadd adbusers
sudo usermod -a -G adbusers $(whoami)
sudo /sbin/udevadm control --reload-rules
sudo systemctl restart systemd-udevd

# Install scrcpy
sudo apt install -y libsdl2-mixer-2.0-0 libsdl2-image-2.0-0 libsdl2-2.0-0 libavformat-dev
wget https://raw.githubusercontent.com/younglim/hats-linux/master/binaries/scrcpy.zip
sudo unzip scrcpy.zip -d /usr/local/share
echo "export PATH=\$PATH:/usr/local/share/scrcpy" >> ~/.bash_profile
source ~/.bash_profile

# Additional requirements for sharing screen over HTTP
sudo apt install xpra python-websockify libcanberra-gtk-module -y 
echo "allowed_users=anybody" | sudo tee --append /etc/X11/Xwrapper.config
wget https://raw.githubusercontent.com/younglim/hats-linux/master/binaries/usr-share-xpra-www.zip
sudo unzip usr-share-xpra-www.zip -d /usr/share/xpra/

# Install Android Emulator
sdkmanager "system-images;android-28;google_apis_playstore;x86_64"
avdmanager create avd --package 'system-images;android-28;google_apis_playstore;x86_64' --name AVDPBIG --device 'pixel_xl'
echo "hw.keyboard=yes" >> ~/.android/avd/AVDPBIG.avd/config.ini 
sudo apt install qemu-kvm -y
sudo adduser $USER kvm
echo 'function emulator { cd "$(dirname "$(which emulator)")" && ./emulator "$@"; }' >> ~/.bash_profile
~/.bash_profile

# Start the Emulator
emulator -avd AVDPBIG
```
