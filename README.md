# How to install psxitarch, enable Wi-Fi, and update the distro

## 1. Get the required files
You need 3 files: An initramfs file, a Linux kernel in form of a "bzImage" file and "psxitarch.tar.xz", which contains the psxitarch distro files. This is the official download page (in Italian language): https://www.psxita.it/psxitarch-linux-v2/

Direct download links (as found on the official website):
- initramfs.cpio.gz: https://mega.nz/#!IUBDiQKY!7WK2zFkUQbqJ02b9LTSAGug3NiL_8XPhprLcqVcfXxQ
- bzImage: https://mega.nz/file/EJhBzTIQ#rpbOcpIpulojUxRUiZjLQ7RqS6tlNc6JmcCrgSxyG-g
- psxitarch.tar.xz: https://www.psxita.it/distro/psxitarch.tar.xz

Note: Some PS4 models do not have working Wi-Fi when using this Linux kernel. A kernel that has drivers for Wi-Fi dongles built-in is available here: https://www.psxita.it/forum/psxitarch-linux-una-distro-per-ps4-t6120-40.html#p45702  
Direct download link:
- bzImage.bin: https://www.mediafire.com/file/amiftcy4lof71ep/bzImage.bin/file

If you want/need to use this kernel instead of the default one, make sure to rename "bzImage.bin" to "bzImage".

## 2. Set up a USB storage device
The device can be a USB stick, an HDD/SDD connected to a USB adapter, an SD card inside a USB SD card adapter, or whatever else that qualifies as "USB storage device". USB 3.0+ is recommended.
Create a single partition on the USB storage device that uses the whole space and format it to FAT32.
Copy the 3 previously downloaded files "initramfs.cpio.gz", "bzImage", and "psxitarch.tar.xz" to the FAT32 partition.

## 3. Boot Linux from a PS4
Plug the USB storage device into USB slot 0, which is the leftmost one up front, and visit a jailbreak website (e.g. for PS4 firmware 7.02 up to 7.55 https://hippie68.github.io). Select the payload "Linux (1 GB)", as it is important not to select "Linux (3 GB)" for the first-time setup, and wait for the jailbreak to succeed. When it does, it will boot Linux from USB.
You have to attach a USB keyboard (+ mouse) to enter the following sections' commands. Use a USB hub if your PS4 doesn't have enough USB slots.

## 4. Install psxitarch
After the boot process is complete, you will see a command line prompt. Enter the following command:
    
    exec install-psxitarch.sh

This script may run for half an hour or longer, depending on your USB device's speed. When the script has finished and you're still on the command line (which can sometimes happen), type:

    resume-boot
   
to boot into the now-installed psxitarch.

If you don't have a US keyboard: Once you see the desktop, set your keyboard layout by clicking the Mushroom in the upper left corner, then "Language" - "Keyboard Layout".

## 5. Connect to the Internet
Click on the Wi-Fi symbol in the upper right corner. Either you're lucky and you own one of the PS4's whose network devices are supported (then connect to your network and proceed to step 6 right away), or you will see the text "No network devices available". Which means you need the alternative kernel mentioned in step 2. With that kernel, you should be able to use some (not all) USB Wi-Fi dongles out-of-the-box. I found a list of Wi-Fi devices that are supposed to have in-built Linux kernel support here: https://github.com/morrownr/USB-WiFi (buy at your own risk!)

If you don't have any Wi-Fi dongles that are supported, you can use a smartphone's USB tethering mode by connecting it via USB (the cable you use must support data transfer - not all do).
Example for Android smartphones (the menu names may vary with different Android versions):

    "Settings" - "Wireless & networks" - "More" - "Tethering & portable hotspot" - "USB tethering" - ENABLE
    
On the PS4, a new network symbol that looks like an Ethernet plug should replace the Wi-Fi symbol, and psxitarch should now have Internet access. Make sure your smartphone is connected via Wi-Fi if you don't want to waste mobile data.

## 6. Update the distro
The distro we have installed right now was released in January 2019. A downside about an Arch Linux installation (which psxitarch is based on) is that the less often you update it, the more difficult it becomes. Psxita has made available update instructions in March 2021 (https://twitter.com/Ps3ita_Team/status/1375799242913157120), however once again it has been a while since then, and those instructions are no longer sufficient. It is still possible to update though, and that is what we will do next.

Click on the Mushroom icon and select "Terminal".

Some currently installed packages would prevent updating, so we will delete them (in the case of jwm-git only temporarily):

    sudo pacman -R jwm-git
    sudo pacman -Rc openobex openexr
    
Confirm with "y" each.
Now and later while using pacman you may see the messages "call to execv failed (No such file or directory)" and "error: command failed to execute correctly". This doesn't matter.

Note: After you have deleted "jwm-git", should you reboot before the whole update process (6.) is complete (which is not advised), you will have no desktop anymore but just a terminal. If it lets you enter text, you can still complete any missing steps below. If not, try to open a new terminal by pressing (CTRL-)ALT-2 (or -3, -4, ...) and log in with user "psxita" and password "changeit".

Next, we need to specify a new key server by entering

    sudo nano /etc/pacman.d/gnupg/gpg.conf

and replacing the old "hkp://pool.sks-keyservers.net" entry, which is now deprecated, with a reliable one, e.g. "hkp://keyserver.ubuntu.com". You can save the file by pressing CTRL-X and confirming with "y".

We also need to change pacman's repo. Open the file /etc/pacman.conf and change the very last line,

    Server = https://psxita.it/repo

to

    Server = https://psxita.it/repo-testing
    
Furthermore, the planned distro update would fail if we don't update the current keyring first. So we enter:

    sudo pacman -Sy archlinux-keyring
    
and confirm each question that looks like ":: Import PGP key [...]?" with "y".

We could now start the distro update, but there is one noteworthy thing left: our current pacman is so old that it won't understand a new keyword ("Path") used by hooks at the end of the update process. Fortunately, there is a statically compiled up-to-date pacman available in Arch Linux's user repository (AUR): https://aur.archlinux.org/packages/pacman-static/

As a binary file is provided there as well, let's download that:

    curl https://pkgbuild.com/~eschwartz/repo/x86_64-extracted/pacman-static -o pacman-static

And make it executable:

    chmod +x pacman-static

Finally, it's time to update the distro:
    
    sudo ./pacman-static -Syu

Note: You could use the currently installed pacman instead, but I can't guarantee that it not understanding the new hooks won't lead to further problems down the road.

No matter which pacman we use, it will now download 900+ MB of updates and install them.
Before and during the update process, answer all questions (should any appear) with "y" and always choose "1" if you are asked to input a number.

To finalize the update, we will need to get jwm (our desktop) back, which we had deleted earlier:

    sudo pacman -S jwm

And that's it. Your psxitarch is now fully up-to-date!

Optional: Having downloaded all those packages now occupies quite some disk space. Which may be undesired if the USB storage device we use is of small capacity. If you want to free the space, type

    sudo pacman -Scc

___

That concludes this How-To. I want to make sure it stays up-to-date. If you find any mistakes or want to add information, please create an issue at https://github.com/hippie68/psxitarch-how-to/issues.

Thanks for reading!
