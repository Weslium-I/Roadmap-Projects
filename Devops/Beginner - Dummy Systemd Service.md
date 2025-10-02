# Beginner - Dummy Systemd Service (In Progress)
https://roadmap.sh/projects/dummy-systemd-service
___
**ℹ️ Info:**
	Create a long-running systemd service that logs to a file.

**📆 Start/Finish Date:** 
	01-10-2025
___
___
## ⚠️ Task
The goal of this project is to get familiar with `systemd`; creating and enabling a service, checking the status, keeping an eye on the logs, starting and stopping the service, etc.

##  ⭐ Requirements
Create a script called `dummy.sh` that keeps running forever and writes a message to the log file every 10 seconds simulating an application running in the background. Here is an example script:

```
#!/bin/bash

while true; do
  echo "Dummy service is running..." >> /var/log/dummy-service.log
  sleep 10
done
```

Create a systemd service `dummy.service` that should start the app automatically on boot and keep it running in the background. If the service fails for any reason, it should automatically restart.

You should be able to `start`, `stop`, `enable`, `disable`, check the `status` of the service, and check the logs i.e. following commands should be available for the service:

```
# Interacting with the service
sudo systemctl start dummy
sudo systemctl stop dummy
sudo systemctl enable dummy
sudo systemctl disable dummy
sudo systemctl status dummy

# Check the logs
sudo journalctl -u dummy -f
```

After completing this project, you will have a good understanding of systemd, creating custom services, managing existing services, debugging issues, and more.

___
___
## ✅  Solution

- Fun start from the get go.
- Tried to update & upgrade `apt`
- Notified I needed to 'modernize' the apt sources
- I did so. I then tried upgrading and it stated that I had malformed entry. fun**.**

**Resolution**
- `sudo reboot -f now` the Linux VM and tried again. No luck.
- Tried googling the issue, it is what is says so I navigated to the problem file to edit it.
- `sudo nano /etc/apt/sources.list.d/ubuntu.sources`
- Found the 'modernize' section and removed it all, saved and exited.
- Tried update/upgrade again.
- Success
- **Bonus** - ssh sign in connection was timing out, turns out the IP had changed to needed to create new access

___

**Actual solution**
```bash
cd
cd Documents
mkdir dummy-sysmd && cd dummy-sysmd

nano dummy.sh
# copied the contents from requirements across
```

Watch me fuck up a bunch
```bash
# I'm on my game today
devops-admin@devops-sandbox:~$ nano dummy.sh
devops-admin@devops-sandbox:~$ dummy.sh
dummy.sh: command not found

devops-admin@devops-sandbox:~$ ./dummy.sh
-bash: ./dummy.sh: Permission denied

devops-admin@devops-sandbox:~$ sudo ./dummy.sh
[sudo] password for devops-admin:
sudo: ./dummy.sh: command not found

devops-admin@devops-sandbox:~$ cd Documents/dummy-sysmd
devops-admin@devops-sandbox:~/Documents/dummy-sysmd$ ls
# Din't even realise I was lost yet

devops-admin@devops-sandbox:~/Documents/dummy-sysmd$ sudo ./dummy.sh
sudo: ./dummy.sh: command not found
devops-admin@devops-sandbox:~/Documents/dummy-sysmd$ sudo dummy.sh
sudo: dummy.sh: command not found
devops-admin@devops-sandbox:~/Documents/dummy-sysmd$ chmod +x dummy.sh
chmod: cannot access 'dummy.sh': No such file or directory
devops-admin@devops-sandbox:~/Documents/dummy-sysmd$ ls

# Finaly found this mf
devops-admin@devops-sandbox:~/Documents/dummy-sysmd$ cd
devops-admin@devops-sandbox:~$ ls
Desktop  Documents  Downloads  dummy.sh  Music  Pictures  Public  snap  Templates  Videos

# Even more struggle smh
devops-admin@devops-sandbox:~$ sudo mv dummy.sh > Documents/dummy-sysmd
-bash: Documents/dummy-sysmd: Is a directory
devops-admin@devops-sandbox:~$ sudo mv dummy.sh > Documents/dummy-sysmd/dummy.sh
mv: missing destination file operand after 'dummy.sh'
Try 'mv --help' for more information.
devops-admin@devops-sandbox:~$ sudo mv dummy.sh Documents/dummy-sysmd/dummy.sh
devops-admin@devops-sandbox:~$ cd Documents/dummy-sysmd/
devops-admin@devops-sandbox:~/Documents/dummy-sysmd$ ls
dummy.sh
```

Finally got it working (I think)
``` bash
devops-admin@devops-sandbox:~/Documents/dummy-sysmd$ cd /var/log/

devops-admin@devops-sandbox:/var/log$ sudo nano dummy-service.log
```
Yep it definitely works (maybe left it going too long)

✅ Create a script called `dummy.sh` that keeps running forever

___

**Next Step** - Time to learn how to create a system service.
Straight to Google for me.

Source: https://linuxhandbook.com/create-systemd-services/

**Systemd service for root user**
- Usually, it is considered a good practice to put these systemd service files inside the `/etc/systemd/system/` directory.
- The systemd service file has three important and necessary sections. They are `[Unit]`, `[Service]` and `[Install]` sections.

First steps - create service file
```bash
devops-admin@devops-sandbox:~/Documents/dummy-sysmd$ cd

devops-admin@devops-sandbox:~$ cd /etc/systemd/system

devops-admin@devops-sandbox:/etc/systemd/system$ sudo nano dummy.service
[sudo] password for devops-admin:
```

File contents:
```
[Unit]
Description=Keeping my sources fresher than Arch Linux
After=multi-user.target

[Service]
ExecStart=/usr/bin/bash /root/.scripts/sys-update.sh
Type=simple

[Install]
WantedBy=multi-user.target
```

**Enabling the service**
``` bash
sudo chmod +x dummy.service

sudo systemctl daemon-reload

sudo systemctl enable dummy.service

# Verify it is enabled
sudo systemctl is-enabled dummy.service
```

**Success**
```
devops-admin@devops-sandbox:/etc/systemd/system$ sudo chmod +x dummy.service
devops-admin@devops-sandbox:/etc/systemd/system$ sudo systemctl daemon-reload
devops-admin@devops-sandbox:/etc/systemd/system$ sudo systemctl enable dummy.service
Created symlink '/etc/systemd/system/multi-user.target.wants/dummy.service' → '/etc/systemd/system/dummy.service'.
devops-admin@devops-sandbox:/etc/systemd/system$ sudo systemctl is-enabled dummy.service
enabled
devops-admin@devops-sandbox:/etc/systemd/system$
```

**Will now reboot the VM and test if it starts automatically**
```
devops-admin@devops-sandbox:/etc/systemd/system$ sudo reboot -f now
Rebooting with argument 'now'.
client_loop: send disconnect: Connection reset
PS C:\Users\****> ssh devops-admin@172.**.***.***
devops-admin@172.**.***.***'s password:
Permission denied, please try again.
devops-admin@172.**.***.***'s password:
Welcome to Ubuntu 25.04 (GNU/Linux 6.14.0-33-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro
Last login: Wed Oct  1 17:58:12 2025 from 172.**.***.***
devops-admin@devops-sandbox:~$ sudo systemctl is-enabled dummy.service
[sudo] password for devops-admin:
enabled
```

✅ Create a systemd service `dummy.service` that should start the app automatically on boot
___

### In Progress
Attempting to use `start`, `stop`, `enable`, `disable`, check the `status` of the service.
