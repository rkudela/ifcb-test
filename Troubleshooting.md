## Cannot mount data directories or instrument data share

If you followed the instructions to mount data directories or the instrument data share on your virtual machine, but are still not seeing the files, there could be several reasons:

1. The share is not up
1. Your `/etc/fstab` configuration is incorrect

### Make sure your share is up

To test if the share is up, try connecting to it from another Windows machine using Windows Explorer. If you can do that, the share is up and should be able to be mounted on the virtual machine. If not, you will need to work on bringing the share up and making sure it is accessible to your Windows host.

### Make sure you can connect to your share

Assuming the share is up, the next step you can try is to try to temporarily connect to your server (either the host with your data directories, or your IFCB) and list the shares.

For example, if your server is named `myserver.wherever.edu` and the username to use to connect to it is `ifcbuser`, run this command:

```
smbclient -L myserver.wherever.edu -Uifcbuser
```

You will be prompted for your password and you should see a list of shares.

Now try to mount the share temporarily. First, create a temporary mount point:

```
mkdir /tmp/sharetest
```

Now, mount it (fill in your own server, share name, username, password, the ones shown here are examples):

```
sudo mount -t cifs -o username=ifcbuser,password=phyto //myserver.wherever.edu/myshare /tmp/sharetest
```

Check the contents with:

```
ls /tmp/sharetest
```

If you see data, there is no issue connecting. Now unmount and delete the temporary mount point:

```
sudo umount /tmp/sharetest && rmdir /tmp/sharetest
```

Now that you are sure connecting works, the problem is likely with your `/etc/fstab` entry.

### Correcting your /etc/fstab entry

Review your `/etc/fstab` file by typing the following command:

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

To edit your `/etc/fstab` with `nano`, run the following command:

```
sudo nano /etc/fstab
```

To learn how to use `nano`, read [this user's guide](http://www.howtogeek.com/howto/42980/the-beginners-guide-to-nano-the-linux-command-line-text-editor/).

Once `/etc/fstab` looks correct, unmount and remount your share like this (in this example, the mount point is `/mnt/ifcb` but you should use whatever you have in your `/etc/fstab` line):

```
sudo umount /mnt/ifcb
sudo mount /mnt/ifcb
```

Don't worry if `umount` says that the share isn't mounted; unmounting it is just a precaution. Now list it, for example:

```
ls /mnt/ifcb
```

If you see data, everything is fine and you have fixed the problem. Otherwise, carefully review `/etc/fstab` to make sure the entry for your share is exactly correct.