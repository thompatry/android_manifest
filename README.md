Establishing a Build Environment  
===
**[Installing the JDK]**  
1. Run the following:  
`sudo apt-get update`  
`sudo apt-get install openjdk-8-jdk`  

**[Update the default Java version]**   
1. Optionally, update the default Java version by running:  
`sudo update-alternatives --config java`  
`sudo update-alternatives --config javac`  

Downloading the Source  
===
**[Installing Repo]**  
1. Make sure you have a bin/ directory in your home directory and that it is included in your path:  
`mkdir ~/bin`  
`PATH=~/bin:$PATH`  
2. Download the Repo tool and ensure that it is executable:  
`curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo`  
`chmod a+x ~/bin/repo`  

**[Initializing a Repo client]**  
1. Create an empty directory to hold your working files. Give it any name you like:  
`mkdir QuantumOS`  
`cd QuantumOS`  
2. Configure git with your real name and email address. To use the Gerrit code-review tool, you will need an email address that is connected with a registered Google account. Make sure this is a live address at which you can receive messages. The name that you provide here will show up in attributions for your code submissions.  
`git config --global user.name "Your Name"`  
`git config --global user.email "you@example.com"`  
3. Run repo init to bring down the latest version of Repo with all its most recent bug fixes. You must specify a URL for the manifest, which specifies where the various repositories included in the Android source will be placed within your working directory.  
`repo init -u git@github.com:QuantumTech/android_manifest.git -b taimen/QC8998_O_8.1.0`  

**[Downloading the QuantumOS Source Tree]**  
1. To pull down the QuantumOS source tree to your working directory from the repositories as specified in the default manifest, run  
`repo sync`  

Preparing to Build  
===
**[Set up environment]**  
1. Initialize the environment with the envsetup.sh script.  
`. build/envsetup.sh`  

**[Choose a target]**
1. Choose which target to build with lunch. The exact configuration can be passed as an argument. For the Pixel 2 XL, use the following commands:  
`lunch quantum_taimen-user`  
`lunch quantum_taimen-userdebug`  
`lunch quantum_taimen-eng`  

**NOTE:** All build targets take the form BUILD-BUILDTYPE, where the BUILD is a codename referring to the particular feature combination.  

| Buildtype | Use |
| --------- | --- |
| user | limited access; suited for production |
| userdebug | like "user" but with root access and debuggability; preferred for debugging |
| eng | development configuration with additional debugging tools |

**[Build the code]**  
1. Build everything with `make`. GNU make can handle parallel tasks with a `-jN` argument, and it's common to use a number of tasks N that's between 1 and 2 times the number of hardware threads on the computer being used for the build. For example, on a dual-E5520 machine (2 CPUs, 4 cores per CPU, 2 threads per core), the fastest builds are made with commands between `make -j16` and `make -j32`.  
`make -j4`  

Running Builds  
===
**[Booting into fastboot mode]**  
1. Fastboot is a bootloader mode in which you can flash a device. During a cold boot of a device, use the following key combinations to boot into fastboot mode:  

| Device | Code name | Keys |
| ------ | --------- | ---- |
| Pixel 2 XL | taimen | Press and hold Volume Down, then press and hold Power. |

**[Unlocking the bootloader]**  
1. For the Pixel 2 XL it is necessary to unlock both the bootloader and critical partitions. Run the following:  
`fastboot flashing unlock`  
`fastboot flashing unlock_critical`  

**NOTE:** the 'flashing unlock' command is only available with fastboot version 23.0.1 or later.  

**[Flashing a device]**  
1. Place the device in fastboot mode by holding the appropriate key combination at boot or using the following command:  
`adb reboot bootloader`  
2. Flash all the generated partition images by running the following commands:  
`fastboot flash boot boot.img`  
`fastboot flash system_a system.img`  
`fastboot flash system_b system_other.img`  
`fastboot flash vendor_a vendor.img`  
`fastboot flash userdata userdata.img`  
`fastboot reboot`  

**NOTE:** If bootloader.img and radio.img are generated, run the following commands before boot.img is flashed:  
`fastboot flash bootloader bootloader.img`  
`fastboot flash radio radio.img`  
`fastboot flash reboot-bootloader`  