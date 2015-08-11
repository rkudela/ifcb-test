## Install prerequisites

Install VirtualBox for Windows Hosts:

https://www.virtualbox.org/wiki/Downloads

Install Github for Windows:

https://windows.github.com/

Install Vagrant for Windows. This will require a reboot.

https://www.vagrantup.com/downloads.html

Open the Git powershell. You should see a prompt and you should be in a sub-directory of your Document directory called "GitHub".

## Get the code

Clone the ifcb-dashboard repository using this command:

```
git clone https://github.com/joefutrelle/ifcb-dashboard.git
```

this will create a directory called ifcb-dashboard. change to that directory using

```
cd ifcb-dashboard
```

Clone the oii repository using this command:

```
git clone https://github.com/joefutrelle/oii.git
```

## Provision and run the virtual machine

Now use vagrant to start the virtual machine. This will take a long time initially, as it must install and configure the dashboard and start it running.

```
vagrant up
```

Once the vagrant operation completes, you should be able to visit the dashboard in a web browser on the Windows host using this URL:

http://localhost:8888/

or the hostname of your Windows machine on your network

http://{your hostname}:8888/

You should see a "blank" dashboard.

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

## TBD: accession