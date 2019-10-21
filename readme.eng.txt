Instructions to independently build firmware for your router from source in Ubuntu LTS

1. Install Oracle VirtualBox on your PC.

2. Download an Ubuntu LTS or Debian distro (it should work in Ubuntu 16.04, Ubuntu 18.04, Debian 8.5 and Debian 9).

3. Create new virtual machine and install the distro from step 2 in it. VirtualBox may use a ISO-image as drive in virtual machine.
	If you plan to use GUI, you must dedicate at least 1.5GB of RAM for the virtual machine. When you select less than 1 GB of RAM you may receive errors when you build the firmware.

4. Start your virtual machine and open a terminal on it.

5. Install git:
	sudo apt-get update 
	sudo apt-get install git

6. Go to your preferred directory (like /home) and run command for create the local copy of repository:
	cd /home/$USER
	git clone https://github.com/Linaro1985/padavan-ng.git
    This copies all the source code, creates a local git-repository. Directory /home/$USER/padavan-ng will be the root of the git-repository.

7. Read the document /home/$USER/padavan-ng/readme.eng.txt and install all the required packages that are listed in it:
	sudo apt-get update
	sudo apt-get install autoconf automake autopoint bison build-essential flex gawk gettext git gperf libtool pkg-config zlib1g-dev libgmp3-dev libmpc-dev libmpfr-dev texinfo python-docutils help2man libtool-bin ncurses-dev libltdl-dev

    mc (Midnight Commander) is not needed to build the firmware, but it will help you navigate through directories, copy or edit files.

8. Go to directory with toolchain sources (cross-compiler and tools for building) and build it:
    cd /home/$USER/padavan-ng/toolchain
    sudo ./clean_sources.sh
    sudo ./build_toolchain.sh

	If you are getting an error about missing Makefile.in, then you should run:
	automake --add-missing
	sudo ./build_toolchain.sh

    The result will be collected the target of toolchain /home/$USER/padavan-ng/toolchain.

	In the future, you will need these commands only if the toolchain will be updated.

9. Now go to directory with sources:
    cd /home/$USER/padavan-ng/trunk

    and edit file /home/$USER/padavan-ng/trunk/.config (this is needed at all times, you need to check that these are set as below):
	#CONFIG_VENDOR=Ralink
	CONFIG_VENDOR=ASUS
	#CONFIG_PRODUCT=RT3883
	CONFIG_PRODUCT=RT-N56U

    Edit path to toolchain (if you need it):
	CONFIG_TOOLCHAIN_DIR=/home/$USER/padavan-ng/toolchain

    To build the firmware, for example, for router RT-N65U uncomment (remove the simbol #) the line:
    CONFIG_FIRMWARE_PRODUCT_ID="RT-N65U"

    and comment the line:
    #CONFIG_FIRMWARE_PRODUCT_ID="RT-N56U"
    Save the file after edit.

10. Clear source tree (every time before a new build)
	sudo ./clear_tree

    Build the firmware:
    sudo ./build_firmware.sh

    Custom firmware file can be found in the directory /home/$USER/padavan-ng/trunk/images.
	VERY IMPORTANT: If you want to save the firmware that you have just created, copy it to another location, because the command clear_tree overwrites the directory images.

12. When repository updated a local source tree must be updated with command:
    sudo git pull

13. If you made any changes to the local repository, when you upgrade tree some files could not be copied. In this case, you must give the command:
    sudo git stash
    sudo git pull

14. If toolchain sources (cross-compiler and tools for building) is changed you must re-build it:
    cd /home/$USER/padavan-ng/toolchain
    sudo ./clean_sources.sh
    sudo ./build_toolchain.sh
