## Overview

This guide covers installation of the IFCB dashboard on Windows 7. To do this you must have access to the internet from your Windows machine, and your Windows machine must have hardware virtualization enabled in the BIOS (most Windows machines have this enabled by default). These instructions have not been tested under Windows 8 or Windows 10, but they will probably work in Windows 8.

## Install prerequisites

Install VirtualBox for Windows Hosts:

https://www.virtualbox.org/wiki/Downloads

The VirtualBox installation will temporarily interrupt network connections to and from your Windows machine.

Install Github for Windows:

https://windows.github.com/

Install Vagrant for Windows. This will require a reboot.

https://www.vagrantup.com/downloads.html

Open the Git powershell. You should see a prompt and you should be in a sub-directory of your Document directory called "GitHub".

## Get the code

In the GitHub powershell, clone the ifcb-dashboard repository using this command:

```
git clone https://github.com/joefutrelle/ifcb-dashboard.git
```

this will create a directory called `ifcb-dashboard`. change to that directory using

```
cd ifcb-dashboard
```

Clone the oii repository using this command:

```
git clone https://github.com/joefutrelle/oii.git
```

## Provision and run the virtual machine

Now use vagrant to start the virtual machine. This will take a long time initially, as it must download, install, configure, and start the dashboard.

```
vagrant up
```

You should be able to visit your dashboard now from the computer you are running it on at the following URL:

```
http://localhost:8888/
```

You will see a "blank" dashboard.

## Add your data

Now you need to give the virtual machine access to your data. The following assumes that your data is on a share called `\\myserver.whatever.edu\ifcbdata`.

At the git shell prompt, enter this command to log in to your virtual machine.

```
vagrant ssh
```

You will see a Linux prompt. Now you need to create a directory that will be used as the path to your share. In this example I'm calling it `/mnt/ifcb`. This kind of directory is known as a "mount point".

```
sudo mkdir /mnt/ifcb
```

Now you need to modify the `/etc/fstab` file to contain a description of how to map your Windows share to the mount point. Here you will need the appropriate username and password for your Windows share. In the following example the username is `ifcbuser` and the password is `planktonarecool`.

```
echo "//myserver.whatever.edu/ifcbdata /mnt/ifcb cifs username=ifcbuser,password=planktonarecool 0 0" \
| sudo tee -a /etc/fstab
```

Now mount the share:

```
sudo mount /mnt/ifcb
```

You should be able to see the contents of the share using this command:

```
ls /mnt/ifcb
```

## Accession

Log in to the dashboard using the "Admin" link at the bottom of the dashboard web interface. The default administrative username is "admin@whoi.edu" and the default password is "12345678". Once logged in, you can, and should change this password on the "Users" tab. You will still have to use the "admin@whoi.edu" login name (changing it is currently unimplemented).

On the administration page, navigate to "Time Series" and click on "Add New Time Series". Give your time series a short name (no spaces) and a brief description, and set "enabled" to "true". Next, click "add path" and enter the path `/mnt/ifcb` (or appropriate subdirectory if your data is in a subdirectory). Make sure that "raw" is selected under the path. The correct directory to add is either one that contains data files, or one that contains a subdirectory for each year that has data.

Click the blue "Save" button. Then click "Accede". Navigate back to the time series by clicking on time series link. Now if you reload repeatedly you should see data appear on the timeline. If it didn't, you may have entered the wrong directory name, and you can navigate back to Admin -> Time Series and click "edit" to change the path and "accede" to restart the data accession process.

## Allowing non-local access to your dashboard

If you would like to be able to connect to your dashboard from a different computer than the one you are running it on, you will need to make a small configuration change to `dashboard_conf.py` in the `ifcb-dashboard` directory.

By default, there is a line in `dashboard_conf.py` that reads:

```
DASHBOARD_BASE_URL='http://localhost:8888/'
```

Edit this, replacing `localhost` with the fully qualified domain name of your computer. For instance if your computer is called `mycomputer.something.edu`, change the line to

```
DASHBOARD_BASE_URL='http://mycomputer.something.edu:8888/'
```

Now restart the dashboard. You will need to do this by being logged into the virtual machine (using `vagrant ssh`) and running the command

```
sudo service apache2 restart
```

If you are running Windows Firewall, you will need to make sure that it is not blocking TCP traffic on port 8888.