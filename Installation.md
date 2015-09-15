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

Open the Git Shell. You should see a prompt and you should be in a sub-directory of your Documents directory called "GitHub".

## Get the code

In the Git Shell, clone the ifcb-dashboard repository using this command:

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
> During this process you may receive a pop-up asking if you want to grant access to networks from "VBoxHeadless". You should answer in the affirmative, to allow your computer to connect to the dashboard virtual machine.

You should be able to visit your dashboard now from the computer you are running it on at the following URL:

```
http://localhost:8888/
```

You will see a "blank" dashboard.

![blank_dashboard_small](https://cloud.githubusercontent.com/assets/2365298/9886208/0f9bb708-5bb7-11e5-8f68-52f543058aa2.png)

## Connect your data directory

Now you need to give the virtual machine access to your data. The following assumes that your data is on a share called `\\myserver.whatever.edu\ifcbdata`.

At the Git Shell prompt, enter this command to log in to your virtual machine.

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

Your data will not yet appear in the dashboard; first you may want to adjust your URL settings to allow non-local access to the dashboard.

> Note: it's very important for the proper functioning of the dashboard for the share to remain accessible at all times. If your share is served from a machine that is rebooted frequently or is not always accessible over the network, then your dashboard may become unresponsive and you will probably need to manually unmount and remount the share on the virtual machine, or reboot it.

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

> Note: don't change `WORKFLOW_URL`.

Now restart the dashboard. You will need to do this by being logged into the virtual machine (using `vagrant ssh`) and running the command

```
sudo service apache2 restart
```

> Note: you will see a warning that begins "Could not reliably determine the server's fully qualified domain name". You can ignore this warning.

If you are running Windows Firewall, you will need to make sure that it is not blocking TCP traffic on port 8888.

## Administration and security

Server administration actions, such as loading data into the dashboard (aka "accession") require logging into the dashboard as a privileged user. The default administrative username is "admin@whoi.edu" and the default password is "12345678". Click on the "Admin" link at the bottom of the web interface, log in, navigate to the "Users" tab, and you can (and should!) change the password for this user from the default.

![set_admin_password_small](https://cloud.githubusercontent.com/assets/2365298/9886299/8ed2d132-5bb7-11e5-83f9-f4e0a35340d8.png)

On the users tab you can also create and modify users, and grant/revoke administrative privileges to a user. You should always have at least one user with administrative privileges that you can log in as.

### Recovering from a forgotten password or lost administrative access

If you forget your password, or you accidentally revoke administrative privileges from all users, or you delete all users, you can use the password reset tool provided. On the virtual machine, run the following command:

```
cd /vagrant; bash password_reset.sh
```

which will create the default administrative user and set the password to a random string that will be shown on the command line. Log in to the dashboard as that user ("admin@whoi.edu"), navigate to "Admin", and change the password, then set up any other users that you would like.

## Accession

On the administration page, navigate to "Time Series" and click on "Add Another Time Series". Give your time series a short name (no spaces) and a brief description, and set "enabled" to "true". Next, fill in the empty path entry with the path `/mnt/ifcb` (or appropriate subdirectory if your data is in a subdirectory). Make sure that default setting of "raw" is selected under the path. The correct directory to add is either one that contains data files, or one that contains a subdirectory for each year that has data.

Click the blue "Save" button (not the "Add Another Path" button). Then click the "Check paths" button. You should receive a message indicating that data has been found at the path you specified. If you see a message that data is not found, you have either set the path wrong or there is a problem accessing your data share.

Once "Check paths" succeeds, click "Accede". Navigate back to the time series by clicking on time series link. Now if you reload repeatedly you should see data appear on the timeline.

> Note: if you put new data in your data directories, the dashboard will not automatically detect and load the data. You will need to press the "Accede" button again to load the new data.

### Debugging accession

If you are having trouble with accession, check that all the following are set up correctly:

* Your data directory share is mounted on your virtual machine (e.g., at /mnt/ifcb) and on the virtual machine, you can list the files on your mount point (e.g., with "ls /mnt/ifcb") and see something (rather than nothing)
* Your time series configuration on the dashboard Admin page has a "raw" path that is set to your mount point (e.g., /mnt/ifcb followed by the appropriate subdirectory on your share)
* Clicking "Check paths" on your time series does not generate a warning about not being able to find data
* You have clicked "Accede"
* You have waited at least 30 seconds and have reloaded the dashboard in your browser

If all of these are taken care of, the next simplest course of action is to reboot your virtual machine. In the Git Shell, in your `ifcb-dashboard` directory, run the following commands:

```
vagrant halt
vagrant up
```

Once your virtual machine is back up, navigate to the dashboard's Admin page and re-attempt accession.

Another approach is to halt the virtual machine, reboot the Windows machine that the virtual machine is running on, and restart the virtual machine.
