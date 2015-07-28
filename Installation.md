Install VirtualBox for Windows Hosts:

https://www.virtualbox.org/wiki/Downloads

Install Github for Windows:

https://windows.github.com/

[TODO: instructions for how to get GitHub for Windows to install the command-line tools]

Install Vagrant for Windows. This will require a reboot.

https://www.vagrantup.com/downloads.html

Open the Git powershell. You should see a prompt and you should be in a sub-directory of your Document directory called "GitHub".

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

now go up one directory and use vagrant to start the virtual machine. This will take a long time initially, as it must install and configure the dashboard and start it running.

```
cd ..
vagrant up
```

Once the vagrant operation completes, you should be able to visit the dashboard in a web browser on the Windows host using this URL:

http://localhost:8888/