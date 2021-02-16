# Installation Notes

Step-by-step list of actions taken from a fresh install to functioning(ish) `muse-lsl` and `eeg-notebooks` installations. 

## Device info

Device: raspberry pi model b (8GB; raspian buster installed from fresh via noobs on a 128gb micro sd card)

```
pi@raspberrypi:~/Code/libraries_of_mine/github/eegnb_rpi $ cat /etc/os-release 
PRETTY_NAME="Raspbian GNU/Linux 10 (buster)"
NAME="Raspbian GNU/Linux"
VERSION_ID="10"
VERSION="10 (buster)"
VERSION_CODENAME=buster
ID=raspbian
ID_LIKE=debian
HOME_URL="http://www.raspbian.org/"
SUPPORT_URL="http://www.raspbian.org/RaspbianForums"
BUG_REPORT_URL="http://www.raspbian.org/RaspbianBugs"
```


## Some general installations and configurations


```
mkdir -p ~/Code/libraries_of_mine/github  
mkdir -p ~/Code/libraries_of_others/github
mkdir -p ~/Software/venvs
```

```
sudo apt-get install vim

sudo apt-get install python3-venv

sudo apt-get install libatlas-base-dev

sudo apt-get install python3-scipy


python3 -m venv ~/Software/venvs/eegnb-rpi

source ~/Software/venvs/eeg-rpi/bin/activate

pip install ipython

pip install pybind11



## Muselsl Installation

in eegnb-rpi environment

pip install -U pip
pip install muselsl


Run muselsl. We get an error:

```
(eegnb-rpi) pi@raspberrypi:~ $ muselsl list
OSError: /home/pi/Software/venvs/eegnb-rpi/lib/python3.7/site-packages/pylsl/liblsl32.so: cannot open shared object file: No such file or directory

```

Solution to this is to do some manual hackery. 


cd ~/Code/libraries_of_others/github
git clone https://github.com/sccn/liblsl
cd liblsl
bash standalone_compilation_linux.sh
cp liblsl.so ~/Software/venvs/eegnb-rpi/lib/python3.7/site-packages/pylsl/liblsl32.so

cd ~/
muselsl list



```

## EEG-Notebooks Installation


