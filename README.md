LineageOS Wiki

# Extracting proprietary blobs from LineageOS zip files

*Intro*
~Extracting proprietary blobs from block-based OTAs
~Extracting proprietary blobs from file-based OTAs


# Introduction

Proprietary blobs can be extracted either from a device already running LineageOS or from a LineageOS installable zip. In this guide we will describe the steps required to extract proprietary files from installable zips.

Before beginning, it is required to know the difference between block-based OTAs and file-based OTAs. In a file-based OTA the content of the system partition is available inside a folder of the zip named system. In a block-based OTA the content of the system partition is stored inside of a file as binary data. If your zip has no system folder or an almost empty system folder, and a file named system.transfer.list in its root, then what you have is a block-based OTAs. Jump to Extracting proprietary blobs from block-based OTAs in this case. If you have the entire content of the system partition inside the system folder and no system.transfer.list, then what you have is a file-based OTA. See Extracting proprietary blobs from file-based OTAs.

~Extracting proprietary blobs from block-based OTAs~

Create a temporary directory and move there:

```sudo mkdir ~/android/system_dump/
   cd ~/android/system_dump/```


Extract system.transfer.list and system.new.dat.br or system.new.dat from the installable LineageOS zip:

```sudo unzip path/to/lineage-*.zip system.transfer.list system.new.dat*```
where ```path/to/``` is the path to the installable zip.



In case system.new.dat.br (a brotli archive) exists, you will first need to decompress it using the brotli utility:

```sudo apt-get install brotli
   brotli --decompress --output=system.new.dat system.new.dat.br```


You now need to get a copy of sdat2img. This script can convert the content of block-based OTAs into dumps that can be mounted. sdat2img is available at the following git repository that you can clone with:

```sudo git clone https://github.com/xpirt/sdat2img```


Once you have obtained sdat2img, use it to extract the system image:

```sudo python sdat2img/sdat2img.py system.transfer.list system.new.dat system.img```


You should now have a file named system.img that you can mount as follows:

```sudo mkdir system/
   sudo mount system.img system/```


After you have mounted the image, move to the root directory of the sources of your device and run extract-files.sh as follows:

```./extract-files.sh ~/android/system_dump/```
This will tell extract-files.sh to get the files from the mounted system dump rather than from a connected device.



Once you’ve extracted all the proprietary files, unmount the system dump and delete the no longer needed files:

```sudo umount ~/android/system_dump/system
    sudo rm -rf ~/android/system_dump/```




~Extracting proprietary blobs from file-based OTAs~

Create a temporary directory to extract the content of the zip and move there:

```sudo mkdir ~/android/system_dump/
    cd ~/android/system_dump/```


Extract the system folder from the zip:

```unzip path/to/lineage-*.zip system/*```
where ```path/to/``` is the path to the installable zip.


After you have extracted the system folder, move to the root directory of the sources of your device and run extract-files.sh as follows:

```./extract-files.sh ~/android/system_dump/```
This will tell extract-files.sh to get the files from the extracted system dump rather than from a connected device. Use chmod 755 extract-files.sh or sh extract-files.sh if permission denied in ./extract-files.sh    .

Once you’ve extracted all the proprietary files, you can delete the files that were extracted from the zip:

``sudo rm -rf ~/android/system_dump/```


© 2016 - 2020 The LineageOS Project
