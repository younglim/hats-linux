# hats for Linux (CentOS/7 and Fedora 27)

Steps to install test automation tools and dependencies for CentOS 7.

## Pre-requisites
- Centos 7.x Minimal
- Run commands as a sudoers user

## Disable password requirement for sudo
`sudo visudo`

Scroll down to the following section
```
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL
```


Use key [d] to remove the `#` from the line:
```
%wheel        ALL=(ALL)       NOPASSWD: ALL
```


Type `:wq!` to save and exit.


## Optional: Enable DHCP client on boot
```
sudo echo 'ONBOOT=yes' >> /etc/sysconfig/network-scripts/ifcfg-enp0s3
sudo echo 'DHCLIENT=yes' >> /etc/sysconfig/network-scripts/ifcfg-enp0s3
```
where `ifcfg-enp0s3` is your network interface

## Recommended: Enable CentOS base repository and update system
```
sudo yum-config-manager --enable base
sudo yum update -y
```

## Disable automount tmpfs at /tmp
```
systemctl mask tmp.mount
```

Reboot the machine with `sudo reboot now` .



## Install wget, zip, unzip, ansible, yum-utils
```
sudo yum install wget zip unzip epel-release yum-utils -y
sudo yum install ansible -y
```

## Install RVM with Ruby
```

sudo yum install -y autoconf automake bison gcc-c++ libffi-devel libtool readline-devel sqlite-devel libyaml-devel openssl-devel
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
\curl -sSL https://get.rvm.io | bash -s stable --ruby
echo "source \$HOME/.rvm/scripts/rvm" >> ~/.bashrc
source ~/.bashrc
sudo yum install git -y
rvm install ruby-head
```


## Install brew
```
source ~/.bashrc
echo -ne "\n" | ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Linuxbrew/install/master/install)"
echo "export PATH=/home/linuxbrew/.linuxbrew/bin:\$PATH" >> ~/.bashrc
source ~/.bashrc
brew --version
```


## Install scrcpy
```
brew install scrcpy
```


## Install Java 8
```
sudo yum install java -y
java -version
```


## Download Android SDK
```
wget https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip
unzip sdk-tools-linux-4333796.zip -d sdk-tools
sudo mkdir /opt/android-sdk
sudo mv `pwd`/sdk-tools/* /opt/android-sdk/
```


## Give permissions to current user
```
sudo chown `whoami`:wheel /opt/android-sdk
```


## Configure env variables for android-sdk
```
echo "export ANDROID_SDK_ROOT=/opt/android-sdk" >> ~/.bashrc
echo "export ANDROID_HOME=/opt/android-sdk" >> ~/.bashrc
echo "export ANDROID_SDK_HOME=~" >> ~/.bashrc
echo "export PATH=\$ANDROID_HOME/platform-tools:\$PATH" >> ~/.bashrc
echo "export PATH=\$ANDROID_HOME/tools:\$PATH" >> ~/.bashrc
echo "export PATH=\$ANDROID_HOME/tools/bin:\$PATH" >> ~/.bashrc
echo "export PATH=\$ANDROID_SDK_ROOT/emulator:\$PATH" >> ~/.bashrc

source ~/.bashrc
mkdir $ANDROID_SDK_HOME/.android
touch $ANDROID_SDK_HOME/.android/repositories.cfg
```


## Install Android build tools
```
yes | sdkmanager --licenses
sdkmanager 'system-images;android-28;google_apis_playstore;x86_64' 'build-tools;28.0.2' 'platform-tools' 'platforms;android-28'

```

## Optional Install Android Emulator (for non-VM environment)
```
sdkmanager "system-images;android-28;google_apis_playstore;x86_64"
avdmanager create avd --package 'system-images;android-28;google_apis_playstore;x86_64' --name AVDPBIG --device 'pixel_xl'
echo "hw.keyboard=yes" >> $ANDROID_SDK_HOME/.android/avd/AVDPBIG.avd/config.ini 
```

## Install ant, maven, gradle
```
sudo yum install ant maven -y
brew install gradle
```


## Install Xvfb
```
sudo yum install psmisc -y
sudo yum install xorg-x11-fonts-Type1 xorg-x11-fonts-75dpi xorg-x11-server-Xvfb -y
```


## Create Xvfb start-stop script
```
sudo nano /opt/xvfb
```

Write to the file:
```
#!/bin/bash

#chkconfig: 345 95 50
#description: Starts xvfb on display 99
if [ -z "$1" ]; then
echo "`basename $0` {start|stop}"
   exit
fi

case "$1" in
start)
   /usr/bin/Xvfb :99 -screen 0 1280x1024x24 &> /var/log/xvfb.log &
;;

stop)
   killall Xvfb
;;
esac
```

```
sudo chmod +x /opt/xvfb
echo "sudo /opt/xvfb start" >> ~/.bashrc
echo "export DISPLAY=:99" >> ~/.bashrc
```


## Install python-pip
```
sudo yum install python-pip python-devel python-xlib -y
```

## Install openssl-devel
```
sudo yum install openssl-devel -y
```

## Install missing JPEG lib 
```
sudo yum install libjpeg-devel libjpeg-turbo-devel -y
```

## Install virtualenv
```
sudo pip install virtualenv
```


## Create hats virtualenv
```
sudo mkdir /opt/hats
sudo chown $(whoami):wheel /opt/hats
virtualenv /opt/hats
source /opt/hats/bin/activate
echo "source /opt/hats/bin/activate" >> ~/.bashrc
```


## Install hats virtualenv
```
source /opt/hats/bin/activate
sudo pip install --upgrade pip
sudo pip install --upgrade setuptools
easy_install functools
pip install python-xlib
source ~/.bashrc
sudo yum install libjpeg-devel # to fix CentOS missing JPEG
pip install -r https://raw.githubusercontent.com/younglim/hats-ci/master/install-list/pip-install-list.txt
```


## Install Google Chrome yum repo
```
sudo bash -c 'cat << EOF > /etc/yum.repos.d/google-chrome.repo
[google-chrome]
name=google-chrome - \$basearch
baseurl=http://dl.google.com/linux/chrome/rpm/stable/\$basearch
enabled=1
gpgcheck=1
gpgkey=https://dl-ssl.google.com/linux/linux_signing_key.pub
EOF'


sudo yum install google-chrome-stable -y

```

## Install Firefox
```
wget http://ftp.mozilla.org/pub/firefox/releases/58.0/linux-x86_64/en-US/firefox-58.0.tar.bz2
sudo tar xvfj firefox-58.0.tar.bz2 -C /opt
echo "export PATH=/opt/firefox:\$PATH" >> ~/.bashrc
```


## Install Chromedriver
```
wget https://chromedriver.storage.googleapis.com/2.41/chromedriver_linux64.zip
unzip chromedriver_linux64.zip -d /opt/hats
echo "export PATH=/opt/hats:\$PATH" >> ~/.bashrc
```

## Install Geckodriver
```
wget https://github.com/mozilla/geckodriver/releases/download/v0.21.0/geckodriver-v0.21.0-linux64.tar.gz
tar -xvzf geckodriver-v0.21.0-linux64.tar.gz -C /opt/hats
```


## Install NVM and Node lts/carbon
```
\curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
. ~/.bashrc
nvm install lts/carbon
```


## Install appium and dependencies
```
sudo yum install gcc-c++ -y
npm install -g appium@1.8.1
```

## Create appium start-stop script
```
sudo nano /opt/appium
```

Write to the file:
```
#!/bin/bash

#chkconfig: 345 95 50
#description: Starts appium
if [ -z "$1" ]; then
echo "`basename $0` {start|stop}"
   exit
fi

case "$1" in
start)
   export NVM_DIR="$HOME/.nvm"
   [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
   [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

   appium &> ~/appium.log &
;;

stop)
   killall -r "^node"
;;
esac
```

```
sudo chmod +x /opt/appium
echo "/opt/appium start" >> ~/.bashrc
```


## Install usbutils
```
sudo yum install usbutils -y
```


## Edit udev to allow usb devices for non-root user
```
wget https://raw.githubusercontent.com/M0Rf30/android-udev-rules/master/51-android.rules
sudo cp 51-android.rules /etc/udev/rules.d/51-android.rules
sudo chmod a+r /etc/udev/rules.d/51-android.rules
sudo groupdel adbusers
sudo groupadd adbusers
sudo usermod -a -G adbusers $(whoami)
sudo /sbin/udevadm control --reload-rules
sudo systemctl restart systemd-udevd
sudo killall adb
adb devices -l
```

## Missing UIAutomator2 or Chromedriver

Remove all npm global packages
```
npm ls -gp --depth=0 | awk -F/ '/node_modules/ && !/\/npm$/ {print $NF}' | xargs npm -g rm
```

Re-install appium
```
npm install -g appium
```
