# Pre-requisites:
- Centos 7.x Minimal
- Run commands as a sudoers user

# Install wget, zip, unzip, ansible
```
sudo yum install wget zip unzip epel-release -y
sudo yum install ansible -y
```

# Install RVM with Ruby
```
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB

\curl -sSL https://get.rvm.io | bash -s stable --ruby

echo "source /usr/local/rvm/scripts/rvm" >> ~/.bashrc
```


# Install brew
```
. ~/.bashrc && echo -ne "\n" | ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Linuxbrew/install/master/install)"

echo 'export PATH="/home/linuxbrew/.linuxbrew/bin:$PATH"' >> ~/.bashrc

. ~/.bashrc && brew --version
```


# Install Java 8
```
sudo yum install java -y
java -version
```


# Download Android SDK
```
https://dl.google.com/android/repository/sdk-tools-linux-3859397.zip
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
echo "export PATH=$ANDROID_HOME/platform-tools:$PATH" >> ~/.bashrc
echo "export PATH=$ANDROID_HOME/tools:$PATH" >> ~/.bashrc
echo "export PATH=$ANDROID_HOME/tools/bin:$PATH" >> ~/.bashrc

mkdir /opt/android-sdk/.android
touch /opt/android-sdk/.android/repositories.cfg
```


# Install Android build tools
```
printf 'y\ny\ny\ny\ny\n' | sdkmanager 'system-images;android-26;google_apis;x86' 'build-tools;26.0.2' 'platform-tools'
```


# Install ant maven gradle
```
sudo yum install ant maven -y
brew install gradle
```


# Install Xvfb
```
yum install psmisc -y
yum install xorg-x11-fonts-Type1 xorg-x11-fonts-75dpi -y
```


# Create Xvfb start-stop script
```
nano /opt/xvfb
```

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
chmod +x /opt/xvfb
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
virtualenv hats
source hats/bin/activate
```


# Install hats virtualenv
```
easy_install functools
pip install -r https://raw.githubusercontent.com/younglim/hats-ci/master/install-list/pip-install-list.txt
```


# Install Google Chrome yum repo
```
nano /etc/yum.repos.d/Google-Chrome.repo
```

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

# Install Chromedriver
```
wget https://chromedriver.storage.googleapis.com/2.35/chromedriver_linux64.zip
unzip chromedriver_linux64.zip -d ~/hats
echo "export PATH=~/hats:$PATH" >> ~/.bashrc
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
