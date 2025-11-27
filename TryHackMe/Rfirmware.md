- Prerequisites
    
    # **Installing the Required Software**
    
    Each year millions of home routers are sold to consumers; a large majority of them don't even know what's running on them. Today we're going to take a look. Before proceeding, we will need a few tools:
    
    - Access to a Linux distribution (Or WSL) with strings and binwalk on it.
    - Linksys WRT1900ACS v2 Firmware found here: [https://github.com/Sq00ky/Dumping-Router-Firmware-Image/](https://github.com/Sq00ky/Dumping-Router-Firmware-Image/)
    - Lastly, [ensure binwalk has JFFS2 support with the following command](https://github.com/ReFirmLabs/binwalk/blob/master/INSTALL.md):
    
    `sudo pip install cstruct;`
    
    `git clone <https://github.com/sviehb/jefferson;`>
    
    `cd jefferson && sudo python setup.py install`
    
    After you've got the tools, you're ready to set up your workspace!
    
    # **Rebuilding the Firmware**
    
    First, we're going to clone the repository that holds the firmware:
    
    `git clone <https://github.com/Sq00ky/Dumping-Router-Firmware-Image/> /opt/Dumping-Router-Firmware && cd /opt/Dumping-Router-Firmware/`
    
    Next, we're going to unzip the **multipart zip file**:
    
    `7z x ./FW_WRT1900ACSV2_2.0.3.201002_prod.zip`
    
    running `ls` ****you should see the firmware image:
    
    `FW_WRT1900ACSV2_2.0.3.201002_prod.img`
    
    Lastly, running a `sha256sum`  on the firmware image you should be left with the value **dbbc9e8673149e79b7fd39482ea95db78bdb585c3fa3613e4f84ca0abcea68a4**
    
    ![](http://puu.sh/HqCnb/5679ac9da1.png)
    
    This doesn’t work right now
    
    So i created a python venv and installed some dependencies as required and then I ran jefferson
    
    `source ~/venvs/jeffenv/bin/activate`
    
    and then only we were able to run jefferson
    
- Investigating the firmware
    
    Using binwalk we got to analyze the firmware
    
    ```jsx
    ┌──(namura㉿namu)-[/opt/Dumping-Router-Firmware]
    └─$ binwalk FW_WRT1900ACSV2_2.0.3.201002_prod.img 
    
    DECIMAL       HEXADECIMAL     DESCRIPTION
    --------------------------------------------------------------------------------
    0             0x0             uImage header, header size: 64 bytes, header CRC: 0xFF40CAEC, created: 2020-04-22 11:07:26, image size: 4229755 bytes, Data Address: 0x8000, Entry Point: 0x8000, data CRC: 0xABEBC439, OS: Linux, CPU: ARM, image type: OS Kernel Image, compression type: none, image name: "Linksys WRT1900ACS Router"
    64            0x40            Linux kernel ARM boot executable zImage (little-endian)
    26736         0x6870          gzip compressed data, maximum compression, from Unix, last modified: 1970-01-01 00:00:00 (null date)
    4214256       0x404DF0        Flattened device tree, size: 15563 bytes, version: 17
    6291456       0x600000        JFFS2 filesystem, little endian
    ```
    
    We saw that on the `6291456` inode offset we had `jffs2` filesystem which may contain the router firmware which we are trying to investigate
    
    We could also extract the files using binwalk using -e flag
    
- Mounting
    
    [flash memory emulation](https://www.notion.so/flash-memory-emulation-21e9c541337a809ea674d9959accf78e?pvs=21)
    
    `rm -rf /dev/mtdblock0`
    
    `mknod /dev/mtdblock0 b 31 0`
    
    Step 2. Create a location for the jffs2 filesysystem to live
    
    `mkdir /mnt/jffs2_file/`
    
    Step 3. Load required kernel modules
    
    `modprobe jffs2`
    
    `modprobe mtdram`
    
    `modprobe mtdblock`
    
    Step 4. Write image to /dev/mtdblock0
    
    `dd if=/opt/Dumping-Router-Firmware-Image/_FW_WRT1900ACSV2_2.0.3.201002_prod.img.extracted/600000.jffs2 of=/dev/mtdblock0`
    
    Step 5. Mount file system to folder location
    
    `mount -t jffs2 /dev/mtdblock0 /mnt/jffs2_file/`
    
    Step 6. Lastly, move into the mounted filesystem.
    
    `cd /mnt/jffs2_file/`
    
    aba ta simple grepping for files and we get the flags
    
    ```jsx
     2102  grep -iRl "ssh" ./ 2>/dev/null                                                        │ 1972  unzip DBMS\\ CSIT-20250616T052216Z-1-001.zip
     2103  grep -iRl "media" ./ 2>/dev/null                                                      │ 1973  cd DBMS\\ CSIT
     2104  vim ./mediaserver.ini                                                                 │ 1976  open Unit\\ 4\\ -\\ The\\ Relational\\ Data\\ Model\\ and\\ Relational\\ Database\\ Constraints.pdf
     2105  grep -iRl "services" ./ 2>/dev/null                                                   │ 1978  ls 
     2106  grep -iRl "default" ./ 2>/dev/null                                                    │ 1979  binwalk -e FW_WRT1900ACSV2_2.0.3.201002_prod.img
     2108  grep -iRl "firmware" ./ 2>/dev/null                                                   │ 1986  tmux
     2109  grep -iRl "version" ./ 2>/dev/null  
    ```