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

I also have a BLED112 bluetooth dongle plugged in throughout. 
 


## Some general installations and configurations

Create some standard folder locations I use

```
mkdir -p ~/Code/libraries_of_mine/github  
mkdir -p ~/Code/libraries_of_others/github
mkdir -p ~/Software/venvs
```

Do global installs for a few programs

```
sudo apt-get install vim libatlas-base-dev
sudo apt-get install python3-venv python3-scipy
```

Create a Python virtual env. We will mostly be staying in here. 

```
python3 -m venv ~/Software/venvs/eegnb-rpi
```

Activate the env

```
source ~/Software/venvs/eeg-rpi/bin/activate
```

Some more python installations

```
pip install -U pip
pip install ipython pybind11
```


## Muselsl Basic Installation

The muselsl installer appears to run without erroring

```
pip install muselsl
```

However things aren't yet working properly

```
(eegnb-rpi) pi@raspberrypi:~ $ muselsl list
OSError: /home/pi/Software/venvs/eegnb-rpi/lib/python3.7/site-packages/pylsl/liblsl32.so: cannot open shared object file: No such file or directory
```


To get round this, the following simple hack seems to work:

```
cd ~/Code/libraries_of_others/github
git clone https://github.com/sccn/liblsl
cd liblsl
bash standalone_compilation_linux.sh
cp liblsl.so ~/Software/venvs/eegnb-rpi/lib/python3.7/site-packages/pylsl/liblsl32.so

cd ~/
```

In other words, re manually compile a `pylsl` C library, rename the output from `liblsl.so` to `liblsl32.so`, and place in the `pylsl` installation folder inside the venv. 

Now, we have success

```
(eegnb-rpi) pi@raspberrypi:~ $ muselsl list
Backend was 'auto' and bluetoothctl was found, using to list muses...
Searching for Muses, this may take up to 10 seconds...
Found device Muse-C2F3, MAC Address 00:55:DA:B0:C2:F3
```

```
(eegnb-rpi) pi@raspberrypi:~ $ muselsl stream
Backend was 'auto' and bluetoothctl was found, using to list muses...
Searching for Muses, this may take up to 10 seconds...
Found device Muse-C2F3, MAC Address 00:55:DA:B0:C2:F3
2021-02-16 01:20:14.213 (  12.585s) [        B6F8CAD0]             common.cpp:54    INFO| v1.14.0-29-g6208017f
Connecting to Muse-C2F3: 00:55:DA:B0:C2:F3...
Connected.
Streaming EEG...
```

So, we are now able to record EEG data on a raspberry pi :)


## Muselsl Graphical Viewer(s)

( to add! ) 


## EEG-Notebooks Installation

In order to get a successful eeg-notebooks installation in the pi, I had to do three things: 

1. Globally install some libraries with `apt-get` and symlink to the `venv` environment
2. Change the dependencies list for `psychopy` (put on a separate branch in my `psychopy` fork)
3. Change the dependencies list for `eeg-notebooks` (put in a separate branch in the main `eeg-notebooks` repo)


Start with the global installs:

```bash
sudo apt-get install python3-wxgtk4.0 python3-sip python3-pyqt5 libhdf5-dev 

ln -s /usr/lib/python3/dist-packages/wx ~/Software/venvs/eegnb-rpi/lib/python3.7/site-packages/

ln -s /usr/lib/python3/dist-packages/sip.cpython-37m-arm-linux-gnueabihf.so ~/Software/venvs/eegnb-rpi/lib/python3.7/site-packages/sip.cpython-37m-arm-linux-gnueabihf.so

ln -s /usr/lib/python3/dist-packages/PyQt5 ~/Software/venvs/eegnb-rpi/lib/python3.7/site-packages/PyQt5
```

Next, initialize the venv:

```bash
source ~/Software/venvs/eegnb-rpi/bin/activate
```

Clone the two repos whose `requirements` I have modified:

```bash
cd ~/Code/libraries_of_mine/github
git clone https://github.com/johngriffiths/psychopy
git clone https://github.com/neurotechx/eeg-notebooks
```

PsychoPy install:

```bash
cd psychopy
git checkout eegnb-rpi-v3.2
pip install -e .
```

EEG-Notebooks install:

```bash
cd eeg-notebooks
git checkout eegnb-rpi
pip install -e .
```

Test that the eeg-notebooks install has worked:

```bash
cd ~/
ipython
```

```python
from eegnb.experiments.visual_n170 import n170
n170.present(duration=10)
```




 


