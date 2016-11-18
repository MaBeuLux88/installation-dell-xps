# installation-dell-xps
Step by step guidelines to setup a dual boot Windows 10 and Debian Testing with i3wm on a brand new Dell XPS 15.
This tutorial contains some specific steps to my company, Zenika.
Feel free to ignore them if they don't apply to you ;-). 

# Starting point and materials
I recently received a new Dell XPS 15 9550 with Windows 10 Pro and Office 2016.
I also have a bunch of old and empty USB sticks available for the Windows 10 Pro, Debian 8 and some drivers I will probably need.
The PC came preinstalled with Windows 10 Pro and a lot of useless stuff preinstalled on it.
The 512GB SSD M.2 PCI-express drive was partitionned no less than 5 times as well which is annoying when you want to install a dual boot in a proper way.
5 partitions according to Windows 10 when you are in the Disk Manager inside Windows 10 but once you are in the installer, their is actually a 6th one! Amen to this.

The target is to : 
- Format the computer and reinstall Windows 10 Pro with roughtly half of the disk space,
- Reinstall Office 2016 and activate it properly,
- Install Debian 8 (stable version) on the other half of the drive and upgrade to the testing version by changing the /etc/apt/sources.list (this is recommended by Debian to do it this way instead of installing directly the testing version unless you want to test the installer itself).

# Save Windows 10 Pro.
Ok first, we have to make sure we saved everything from the Windows 10 Pro to reinstall it.
By the way, the default user is "PC" and the password is "zenika".

## Windows Product Key
With https://www.magicaljellybean.com/keyfinder/ I got the Windows 10 Pro product key. Feel free to use another software if you want to.
Normally I should not need it because Windows 10 was already installed on this machine, obviously, so the Microsoft product validation servers can find my computer and activate it for me.
For safety purposes, I prefer to save the key.
Moreover in Windows 10, you can attach a Microsoft Account to the computer.
You can check on https://account.microsoft.com/ that your computer appears correctly in this page once logged in.

## Create the USB Bootable Stick with Windows 10.
Go to https://www.microsoft.com/en-us/software-download/windows10/
Here you will find a "Download tool now" button and you will get a MediaCreationTool.exe file.
Find a 4Go USB Stick (at least), run the tool and follow the instructions.
When asked to select which version of Windows you want, select "Windows 10". The Windows 10 *Pro* is not missing, it's just that both versions are actually embedded together.
The process takes some time (can be speed up with a good ethernet bandwidth) but it works surprisingly well.

## Microsoft Office 2016
If your computer was installed by the StimPlus team, the product key of Office 2016 has been used online on a separated Microsoft Office Account.
Ask the StimPlus team for this login/password. It should be provided to you in an Excel file.
I presonnaly found this file on the computer itself on the Desktop in a "Paramètres" folder.
If you don't have it, ask Cécile.
Check that you can access this account on https://office.com/myaccount.

By the way, it's NOT possible to transfer this licence to another Microsoft Account so you will have to keep both.
Yes it's retarded, but it's Windows policy so what did you expect...?

Double check that you have the "Install" button and the "Microsoft Office Famille et Petite Entreprise 2016" licence on the website.
We will use this to reinstall Office once we are done reinstalling obviously.

For the record, I also have in this folder a file that seems to be the Symantec Endpoint Protection Product Key... I saved it as well.

# Create the USB Bootable Stick with Debian
I used my other computer (already on Debian) to create the USB stick with this procedure : https://www.debian.org/releases/jessie/amd64/ch04s03.html.en
So basically for me it was : 
root $> cp debian-netinst.iso > /deb/sdb
root $> sync

# Format Windows 10 and reinstall Windows 10
So to sum up, now I have : 
- Windows 10 Product Key and my Microsoft Account knows my computer, 
- Office 2016 Product Key on the second Microsoft Office Account,
- Windows 10 & Debian 8 bootable USB Sticks.

So now I'm ready to re-install Windows 10.

Warning : It's going to fail miserably... So make sure to read ahead before processing to get the solution...

After a quick check in the BIOS (spam F12 when the PC starts), I haven't changed much things. Some details but nothing fancy.
I ran into a problem when doing this for the first time : my SSD was not detected during the Windows 10 installation.
My PC only have a 512GB M.2 PCI-e SSD so I don't need a RAID driver.
In this BIOS, under "System Configuration" > "SATA Operation" change "RAID On" to "AHCI".

Warning : After doing this, you won't be able to boot on the previous Windows 10 unless you switch it back.

Insert the Windows 10 USB and restart the system. F12 to boot on the key.
Select the languages, click Install Now.
Accept the licence and choose "Custom: Install Windows Only (advanced)" option.
Now you are at the Disk Partition step (where I had no disk previously!).
Delete all the 6 partitions you see and create a new one with the space you want Windows to take.
Windows will create 4 partitions. Why ? Because Windows... That's why...
Click on the 4th partition (the big one) and click next.

Then wait a bit and enjoy the wonderful error : 
"Windows cannot install required files. Make sure all files required for installtion are available, and restart the installation. Error Code: 0x8007025D."
In other word : "I'm a fucking bad developper. Some random shit happened in my code because I'm bad so here : Take this random number and good luck with the rest of your life".
...

EDIT : Apparently my USB stick MIGHT be fucked up even if it was able to boot the Windows ISO pretty well so far... Maybe this is working with a proper USB stick.

Google and forums didn't help me very much on this matter so after multiple tries, I switched for a new strategy...
- On another Win 7 PC, I got the MediaCreationTool once again,
- I downloaded the windows ISO instead of building a USB stick directly,
- I changed for a new USB stick,
- I downloaded rufus-2.11p.exe which is a portable tool to build bootable ISO on USB sticks,
- I ran this tool over my new USB stick to install my Windows 10 ISO.

I left all the parameters by default : NTFS + Type de partition MBR pour BIOS ou UEFI. I just attached the ISO and started it.

Back on my XPS, I could not detect the USB stick...
In the menu you get after the F12, I change the Boot option to the Legacy BIOS with no secure boot instead of the UEFI with secure boot.
Note: Apparently Debian doesn't support secured boot anyway so... That's another problem solved in advance.

Now with the legacy bios I can detect the USB stick and it boot correctly.
This time I could follow all the installation step by step and disable all the Microsoft spywares during the installtion process...
The only other parameter I changed really is the size of the partition during the partitionning process to leave a blank space at the end of my disk for Debian.
SUCCESS !!!

Now Windows 10 is booting correctly.
The difference I noticed with the previous try in UEFI with the other USB stick was during the partitioning process.
With the UEFI I got 4 partitions as stated earlier : 3 small ones and the main one of course.
With the Legacy BIOS, I got only 2 partitions : The small MBR and the main one... I don't mind one or the other so I just carried on like this.
I don't understand why the UEFI mode needs 4 partitions to work properly. Maybe I should read a bit about that some day.

# Installation of Debian
It would not have been fun without a problem, right!?

Before I jump into the explanation, just to make sure everyone is following me:  
- I have 1 little MBR partition for Windows.
- I have 1 big primary partition (~240GB) for Windows 10.
- I have 1 empty space after that for my Debian installation.

During the installation of Debian, I used the Graphical Install process as I always do.
As I'm using a netinst installation, I plugged in my computer to my home rooter with an ethernet cable.
My computer doesn't have a native ethernet plug so I used my DA200 dongle (which goes in my USB-C plug, not the USB 3.0 one).
Everything was running smoothly until the moment when the system needs to access the package on the Internet.

The error was something like "Bad archive mirror".
Some research on the internet didn't help so I always I was... AAAAAALLLLLL BYYYY MYYYYY SEEEeeeEEEELLLF
During the installation of debian, you can access to some terminals using ctrl + alt + 1-2-3-4.
In one of these, I could run 2 commands : 
 - ping google.com => KO
 - ping 8.8.8.8 => OK
So apparently, I have internet but no DNS resolution... I'm fucking doomed... There is no other way I think...

Anyway, after multiple tries and some vaudou invocation, I found a plan B.
I still have no idea why I could not resolve these DNS... Maybe Numericable was broken, maybe I'm just doomed...
My plan B was to unplug the ethernet and use my telephone as a USB rooter to get another internet connection.

This time, it worked as expected and I could finish the installation of Debian without any other problem.
Once the system is up and running, I unstalled my usual system and so far so good.

For the record, I don't use a Display Manager (= the login screen where you type your password).
I boot directly in the default linux consol where I can type my login + password.
Then I type directly the "startx" command to start i3wm.
 - apt-get install i3wm xinit

#Problem I still have today
Globally, everything is working on my machine.
The native HDMI port on the machine is working full HD.
Ethernet + USB is working on the dongle DA200.
Apparently there is a bug in the current version of the kernel I have about the dongle : I can't have more than 1440x900 on the HDMI port of the DA200 dongle. 
If I try a bigger resolution, it doesn't work...
https://bugs.freedesktop.org/show_bug.cgi?id=94567

Another mystery is still undergoing some tests : The VGA port on my DA200 dongle doesn't work when I'm at SFR with Ethernet + USB keyboard on the dongle but it works at Zenika with JUST the VGA plugged in on the 1280x800...

I will let you know when the mystery is solved... 
My VGA cable at SFR is currently my main suspect ;-) but I'm not going there until next Friday.
By the way, at work I have 2 old full HD screens (DVI-D + VGA but no HDMI) so my plan was to plug one screen with a HDMI -> DVI-D cable and the other one with VGA from the dongle -> VGA.

Let me know if you want some help :D !
