---
title: Getting Started
---

### Installation

Qiling Framework is written in Python programming language and it works with different operating system and not limiting to any CPU architecture.

For this installation guide, Ubuntu desktop 18.04.3 LTS 64bit is the base example (Qiling Framework works in other Linux distributions that run Python 3.5 and above). Grab a copy of official Ubuntu ISO images from [Ubuntu CD mirrors](https://launchpad.net/ubuntu/+cdmirrors). Update and the system and also install pip3, git and cmake
```
sudo apt-get update
sudo apt-get upgrade
sudo apt install python3-pip git cmake
```

Once completed, clone a copy of Qiling Framework source from github and run setup to install it.
```
git clone https://github.com/qilingframework/qiling
cd qiling
sudo pip3 install -r requirements.txt
sudo python3 setup.py install 
```

If you are using pyenv, run the command shown below.
```
mv $(dirname $(which python))/python2.7 $(dirname $(which python))/python2.7.bak
pip install -r requirements.txt
python setup.py install
```

---

#### Important note on Windows DLLs and registry

Due to distribution restriction, Qiling Framework will not bundle Microsoft Windows DLL files and registry. Please copy respective DLLs and registry from Microsoft Windows System. For Windows 10 usually found in C:\Windows\system32 (64bit dll) and C:\Windows\SysWOW64 (32bits dll) and place them in $rootfs/dlls

We also included a dlls "collector" script, copy into Windows and it will start to collect dlls and registrty
```
qiling/examples/scripts/dllscollector.bat
```

Any other dlls and registry references, as below:

For 32bit Windows dlls, please refer to [DLLX86.txt](https://github.com/qilingframework/qiling/blob/master/docs/DLLX86.txt) for Windows 32bit DLLs hashes and file version

For 64bit Windows dlls, please refer to [DLLX8664.txt](https://github.com/qilingframework/qiling/blob/master/docs/DLLX8664.txt) for Windows 64bit DLLs hashes and file version

Additional Notes: .travis.yml will be able to clearly list out dlls needed

To export Windows Registry from Windows
```
ntuser hive C:\Users\Default\NTUSER.DAT 
reg save hklm\system SYSTEM
reg save hklm\security SECURITY
reg save hklm\software SOFTWARE
reg save hklm\SAM SAM
```
---

#### Notes on macOS >= 10.14

Keystone-engine compilation from py-pip fails (on Mojave at least) because i386 architecture is deprecated for macOS. 

```
CMake Error at /usr/local/Cellar/cmake/3.15.4/share/cmake/Modules/CMakeTestCCompiler.cmake:60 (message):
  The C compiler

    "/Library/Developer/CommandLineTools/usr/bin/cc"

  is not able to compile a simple test program.

  It fails with the following output:
```

A temporary workaround is to install keystone-engine from source:
* Remove `keystone-engine>=0.9.1.post3` line from `requirements.txt`
* Install keystone-engine Python binding from source:
```
git clone https://github.com/keystone-engine/keystone
cd keystone
mkdir build
cd build
../make-share.sh
cd ../bindings/python
sudo make install
```

Once completed workaround installation, run Qiling Framework setup.

---

### Qiling Framework on Docker

If quick and easy way to deploy Qiling Framework is preferred, spin it with docker container.

i. Building Qiling Framework docker image

Building the Qiling Framework docker by running command below within source directory.

```
docker build -t qiling:1.0 .
```

ii. Running Qiling Framework docker with a bind mount

Required DLLs can be bind-mounted to Qiling Framework container. Presuming DLLs are located in /analysis/win.

```
docker run -dt --name qiling \
 -v /analysis/win/x86dlls:/qiling/examples/rootfs/x86_windows/dlls \
 -v /analysis/win/x8664dlls:/qiling/examples/rootfs/x8664_windows/dlls \
 qiling:1.0
```

```
docker exec -it qiling bash
```

Docker container port can be published with -p switch. This is useful for emulating service such as httpd of a router.
