# hats for Linux (CentOS/7)


# Pre-requisites
- Centos 7.x Minimal
- Run commands as a sudoers user

# Optional: Enable DHCP client on boot
```
sudo echo 'ONBOOT=yes' >> /etc/sysconfig/network-scripts/ifcfg-enp0s3
sudo echo 'DHCLIENT=yes' >> /etc/sysconfig/network-scripts/ifcfg-enp0s3
```
where `ifcfg-enp0s3` is your network interface

# Recommended: Enable CentOS base repository
```
sudo yum-config-manager --enable base
```


# Install wget, zip, unzip, ansible, yum-utils
```
sudo yum update -y
sudo yum install wget zip unzip epel-release yum-utils -y
sudo yum install ansible -y
```

# Install RVM with Ruby
```
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
\curl -sSL https://get.rvm.io | bash -s stable --ruby
echo "source ~/.rvm/scripts/rvm" >> ~/.bashrc
```


# Install brew
```
source ~/.bashrc
echo -ne "\n" | ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Linuxbrew/install/master/install)"
echo 'export PATH="/home/linuxbrew/.linuxbrew/bin:\$PATH"' >> ~/.bashrc
source ~/.bashrc
brew --version
```


# Install Java 8
```
sudo yum install java -y
java -version
```


# Download Android SDK
```
wget https://dl.google.com/android/repository/sdk-tools-linux-3859397.zip
unzip sdk-tools-linux-3859397.zip -d sdk-tools
sudo mkdir /opt/android-sdk
sudo mv `pwd`/sdk-tools/* /opt/android-sdk/
```


# Give permissions to current user
```
sudo chown `whoami`:wheel /opt/android-sdk
```


# Configure env variables for android-sdk
```
echo "export ANDROID_SDK_ROOT=/opt/android-sdk" >> ~/.bashrc
echo "export ANDROID_HOME=/opt/android-sdk" >> ~/.bashrc
echo "export ANDROID_SDK_HOME=/opt/android-sdk" >> ~/.bashrc
echo "export PATH=\$ANDROID_HOME/platform-tools:\$PATH" >> ~/.bashrc
echo "export PATH=\$ANDROID_HOME/tools:\$PATH" >> ~/.bashrc
echo "export PATH=\$ANDROID_HOME/tools/bin:\$PATH" >> ~/.bashrc

source ~/.bashrc
mkdir /opt/android-sdk/.android
touch /opt/android-sdk/.android/repositories.cfg
```


# Install Android build tools
```
yes | sdkmanager --licenses
sdkmanager 'system-images;android-26;google_apis;x86' 'build-tools;26.0.2' 'platform-tools'
```


# Install ant, maven, gradle
```
sudo yum install ant maven -y
brew install gradle
```


# Install Xvfb
```
sudo yum install psmisc -y
sudo yum install xorg-x11-fonts-Type1 xorg-x11-fonts-75dpi xorg-x11-server-Xvfb -y
```


# Create Xvfb start-stop script
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


# Install python-pip
```
sudo yum install python-pip python-devel python-xlib -y
```


# Install virtualenv
```
sudo pip install virtualenv
```


# Create hats virtualenv
```
sudo mkdir /opt/hats
chown `whoami`:wheel /opt/hats
virtualenv /opt/hats
source /opt/hats/bin/activate
echo "source /opt/hats/bin/activate" >> ~/.bashrc
```


# Install hats virtualenv
```
source /opt/hats/bin/activate
easy_install functools
pip install python-xlib
pip install -r https://raw.githubusercontent.com/younglim/hats-ci/master/install-list/pip-install-list.txt
```


# Install Google Chrome yum repo
```
sudo nano /etc/yum.repos.d/Google-Chrome.repo
```

Write to the file:
```
[google-chrome]
name=google-chrome
baseurl=http://dl.google.com/linux/chrome/rpm/stable/$basearch
enabled=1
gpgcheck=1
gpgkey=https://dl-ssl.google.com/linux/linux_signing_key.pub
```

```
yum info google-chrome-stable
sudo yum install google-chrome-stable -y
```

# Install Firefox
```
wget http://ftp.mozilla.org/pub/firefox/releases/58.0/linux-x86_64/en-US/firefox-58.0.tar.bz2
sudo tar xvfj firefox-58.0.tar.bz2 -C /opt
echo "export PATH=/opt/firefox:\$PATH" >> ~/.bashrc
```


# Install Chromedriver
```
wget https://chromedriver.storage.googleapis.com/2.35/chromedriver_linux64.zip
unzip chromedriver_linux64.zip -d ~/hats
echo "export PATH=~/hats:\$PATH" >> ~/.bashrc
```

# Install Geckodriver
```
wget https://github.com/mozilla/geckodriver/releases/download/v0.19.1/geckodriver-v0.19.1-linux64.tar.gz
tar -xvzf geckodriver-v0.19.1-linux64.tar.gz
```


# Install NVM and Node lts/carbon
```
\curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
. ~/.bashrc
nvm install lts/carbon
```


# Install appium
```
npm install -g appium
```

# Create appium start-stop script
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
	   kill $(ps aux | grep 'appium' | awk '{print $2}')
	;;
	esac

```

```
sudo chmod +x /opt/appium
echo "/opt/appium start" >> ~/.bashrc
```


# Install usbutils
```
sudo yum install usbutils -y
```


# Edit udev to allow usb devices for non-root user
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
