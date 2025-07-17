


## 1 Linux Fundamentals


**1. What file contains a list of all network-accessible services and their related ports?**

**`/etc/services`**

**2. Which action will display your hardware device profile on your Linux system?**

Type **Ishw** from the terminal.

**3. You must create a directory nameaptd **spreadsheets** in the **Documents** directory belonging to user steve. Your current working directory (PWD) is /home/steve. What command would you USE?**

**`mkdir Documents/spreadsheets`**

4. Using systemd tools, how would you set the default runlevel so the machine automatically boots to a graphical user interface (GUI)

**`systemctl set-default graphical.target`**


**5.What is an advantage of using tar instead of zip ?**

**tar preserves Unix filesystem features.**

**6. What file contains the system and user created groups?**

**`/etc/group`**


**7. When using Debian or Red Hat systems, which package must you install and configure to allow for incoming Secure Shell (SSH) connections to Linux systems?**

**`openssh-server`**


**8. What tool does the Debian manual recommend you use to create a new user when working on Debian/Ubuntu-based systems?**

**adduser**

**9. You must write a shell script to repeat a command once every hour and only stop if you kill or interrupt the script. What action should you take to best accomplish this?**

**`Use a while loop with an hour long sleep timer.`**

**10. You are reviewing the output from running netstat and notice a port 21 is active and listening. What would you deduce from this information?**

**`A File Transfer Protocol (FTP) daemon is running on the local host.`**

**11. You plan to build your own Ubuntu server image for a cloud deployment. What is the minimum disk space you will need for the operating system (OS) itself?**

**4 GB**

**12. What command could a non-root user issue to update Debian/Ubuntu repositories?**

**sudo apt update**

**13. Which code will print a complete, easy-to-read list of the Peripheral Component Interconnect (PCI) devices connected to your machine?**

**1spci**

**14. What application allows you to install third-party software on an Ubuntu system?**

**Advanced Package Tool (APT)**

**15. What folder contains most of the human-editable configuration files that control how applications operate?**

**/etc**


**16. You just installed a new Peripheral Component Interconnect (PCI) network card and Linux did not immediately recognize it. What tool do you need to enable the kernel module to run the card?**

**modprobe module-name**

**17. On most modern Linux systems, what file contains the list of users and encrypted versions of their passwords?**

**`/etc/shadow`**


**18. How would you adjust the display resolution in distributions using the GNOME 3 desktop (but not the Wayland compositor) to 1366x768 using the command line?**

**`xrandr --size 1366x768`**

**19. Using systemd tools, how would you change the current target to allow only command-line access?**

**`systemctl isolate multi-user.target`**


**20. Which Linux desktop is best suited for older, slower, or low-powered hardware?**

**`XForms Common Environment (Xfce)`**

**21. Which flag would you add to a useradd command in a CentOS system if you wanted to add new users to one or more additional groups?**

`-G`

**22. At a minimum, which is a valid, standalone, tar command?**

```
tar -cf archive. tar foo.txt
```

**23. In a Bash shell, what would you type to set an environment variable of EDITOR to nano and add the setting to your environment?**

```
EDITOR=/usi/bin/nano; export EDITOR
```

**24. What command dumps socket connection statistics?**

```
ss
```

**25. What command lists only directories in the /etc directory?**

```
ls -d /etc/*/
```

**26. What setting must you edit on the server-side machine to allow remote graphic sessions over Secure Shell (SSH)?**

**The `X11Forwarding` line in the `/etc/ssh/sshd_config` file**

**27. When using CentOS, which file contains the configurations controlling way the useradd command will define system and user account unique identifiers (UIDs)?**

**`/etc/login.defs`**


**28. What command would you use to change a file's group to root for a file named users.txt?**

**`sudo chgrp root users.txt`**

**29. Which command would you use to determine the distribution name and version of your Linux system?**

**`cat /etc/os-release`**


30 You plugged a USB drive into your Ubuntu system with automount enabled and want to check its contents. What should you do to accomplish this? 

**Navigate to your username in the media directory.**

31 Which command will update the password for a user named "user"?

**passwd user**

32 What are the three types of object owners on a Linux system?

**User (owner), group, and other**

33 How, on a system using systemd, would you set (or reset) your computer's timezone to Eastern Standard Time?

**sudo timedatectl set-timezone EST**

34 What setting in the /etc/passwd file is normally given to system user accounts (meaning, an account associated with a process rather than a person) to prevent them from being used to login?

**/usr/sbin/nologin**

35 What is the purpose of the shebang line in a Linux script (for example, #!/bin/bash)?

**To identify the shell interpreter you want to use**

36  What command allows you to set a priority value for a process when it launches?

**nice**

37 Which file sets the PATH environment variable for the current user on an Ubuntu system?

**`~/.profile`**

38  On a Debian/Ubuntu system, how would you delete a package along with any configuration settings?

**sudo apt purge packagename**

39  Which code will print a complete, easy-to-read list of the Peripheral Component Interconnect (PCI) devices connected to your machine?

**lspci**

40 In addition to loading the operating system (OS), what other purpose might a boot loader serve?

**To provide a multi-OS boot environment**

41 What command displays all processes in a tree format?

**pstree**

42 What command displays a static snapshot of running processes?

**ps**

43 What is firmware?

**Software embedded into the system hardware**

44 What command assigns others read, write, and execute (rwx) permissions to the file users.txt?

**chmod o+rwx users.txt**

45 What command clears the local APT-based repositories of retrieved package files on a Debian/Ubuntu system?

**sudo apt-get clean**

46 What User ID will the root account possess by default?  **0**

47 What is the default nice setting for a new process in Linux?  **0**

48 What command would you issue to display all the groups associated with the system?

**cat /etc/group**

49 You download the ISO file for the Linux distribution you want to install and use on a local PC or server. What is your next step?

**Write the image to a DVD or USB drive.**

50 After you update the system repository index (using apt update), what would you execute to apply the updates on Ubuntu?

**sudo apt upgrade**

51 What command would you issue to download and install VideoLAN Client (VLC) in Ubuntu

**sudo apt-get install vlc**

52 When would you install the Gnome Tweak Tool on your Linux system? 

**When you want to customize your system's user interface**

53 What is the most widely-used default boot loader for Linux systems?

**Grand Unified Boot Loader (GRUB)**

54 Which statement best explains what happens when you use a USB device to run a live image of Linux on your computer?

**You can run a single session using a USB or optical drive without modifying your local system.**

55 You must list the permission and ownership of the file users.txt. What would you do to accomplish this task?

Issue ls -l users.txt


