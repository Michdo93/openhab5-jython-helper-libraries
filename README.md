# Installation

## Required tools

At first you have to install:

```
sudo apt install -y git sshpass
```

## Cloning the repository

```
cd ~
git clone https://github.com/Michdo93/openhab5-jython-helper-libraries
```

## Installing the Jython Scripting binding

```
sshpass -p habopen ssh -p 8101 -o StrictHostKeyChecking=accept-new openhab@localhost 'exit'
sshpass -p habopen ssh -p 8101 -o StrictHostKeyChecking=no openhab@localhost 'feature:install openhab-automation-jythonscripting'
```

## Configuring Python Path

You have to run:

```
sudo systemctl stop openhab
sudo sed -i '/^EXTRA_JAVA_OPTS=/d' /etc/default/openhab && echo -e 'EXTRA_JAVA_OPTS="\n  -XX:+UnlockExperimentalVMOptions\n  -XX:+EnableJVMCI\n  -XX:+UseG1GC\n  -Dpolyglot.engine.WarnInterpreterOnly=false\n  -Dfile.encoding=UTF-8\n  -Xms256m -Xmx1024m\n  -Dpython.NativeModules=false\n  -Dpython.ForceIsolation=false\n  -Dpython.home=/usr/share/openhab/runtime/lib/python\n  -Dpython.path=/etc/openhab/automation/jython/lib:/etc/openhab/automation/jython/modules\n"' | sudo tee -a /etc/default/openhab
sudo rm -rf /var/lib/openhab/tmp/* && sudo rm -rf /var/lib/openhab/cache/*
```

You should see something linke this:

```
EXTRA_JAVA_OPTS="
  -XX:+UnlockExperimentalVMOptions
  -XX:+EnableJVMCI
  -XX:+UseG1GC
  -Dpolyglot.engine.WarnInterpreterOnly=false
  -Dfile.encoding=UTF-8
  -Xms256m -Xmx1024m
  -Dpython.NativeModules=false
  -Dpython.ForceIsolation=false
  -Dpython.home=/usr/share/openhab/runtime/lib/python
  -Dpython.path=/etc/openhab/automation/jython/lib:/etc/openhab/automation/jython/modules
"
```

As you can see, performance optimization for GraalVM and GraalPython has also been carried out here, as it is recommended to migrate to Python 3 binding. However, this requires that GraalVM is also used for openHAB 5.

## Copying the openHAB Helper Libraries to your automations folder

```
cd ~/openhab5-jython-helper-libraries
sudo cp -r automation/ /etc/openhab
sudo chown -R openhab:openhab /etc/openhab/automation
sudo systemctl daemon-reload
sudo systemctl start openhab
```

## Installing Python 2.7 and pip2

```
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential libffi-dev libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl
sudo apt install -y build-essential checkinstall libncurses-dev libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev libffi-dev
wget https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tar.xz
tar xf Python-2.7.18.tar.xz
cd Python-2.7.18
./configure --enable-optimizations --prefix=/usr/local
make -j$(nproc)
sudo make altinstall
sudo apt install -f -y
curl https://raw.githubusercontent.com/Michdo93/get-pip/main/get-pip.py -o get-pip.py
python get-pip.py
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2.7 0
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 1
sudo update-alternatives --config python
```





