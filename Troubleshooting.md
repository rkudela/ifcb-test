## Cannot mount data directories or instrument data share

If you followed the instructions to mount data directories or the instrument data share on your virtual machine, but are still not seeing the files, there could be several reasons:

1. The share is not up
1. Your `/etc/fstab` configuration is incorrect

To test if the share is up, try connecting to it from another Windows machine. If you can do that, the share is up and should be able to be mounted on the virtual machine. If not, you will need to work on bringing the share up and making sure it is accessible to your Windows host.

Assuming the share is up, the next step you can try is to try to temporarily connect to your server (either the host with your data directories, or your IFCB) and list the shares.

For example, if your server is named `myserver.wherever.edu` and the username to use to connect to it is `ifcbuser`, run this command:

```
smbclient -L myserver.wherever.edu -Uifcbuser
```

You will be prompted for your password and you should see a list of shares. If that works, then there are no issues connecting, and the problem is likely with your `/etc/fstab` entry. Review your `/etc/fstab` file by typing the following command:

```
cat /etc/fstab
```

You should see one or more lines that you added earlier in the installation process. For example if you ran the command

```
echo "//myserver.whatever.edu/ifcbdata /mnt/ifcb cifs username=ifcbuser,password=planktonarecool 0 0" \
| sudo tee -a /etc/fstab
```

then your `/etc/fstab` file should contain the line

```
//myserver.whatever.edu/ifcbdata /mnt/ifcb cifs username=ifcbuser,password=planktonarecool 0 0
```

If there are errors in `/etc/fstab`, you will need to edit that file on the virtual machine using a text editor such as `nano`, which is pre-installed with Linux.

> Note: it is critically important to only edit the lines that you have added to `/etc/fstab`. If you edit the other line(s), you could make the virutal machine unable to boot.

Refer to [[Installation]] to see what the correct `/etc/fstab` entry should look like for your share.