# Simple Monitoring - In Progress
https://roadmap.sh/projects/simple-monitoring-dashboard
___
**Info:**
	Set up a basic monitoring dashboard using Netdata.

**Start Date:** 
	30-09-2025
___
___
## Task
The goal of this project is to learn the basics of monitoring. It is to help you understand how to monitor the health of a system and how to set up a basic monitoring dashboard.

## Requirements
In this project, you will set up a basic monitoring dashboard using [Netdata](https://github.com/netdata/netdata). Netdata is a powerful, real-time performance and health monitoring tool for systems and applications.

- Install Netdata on a Linux system.
    
- Configure Netdata to monitor basic system metrics such as CPU, memory usage, and disk I/O.
    
- Access the Netdata dashboard through a web browser.
    
- Customize at least one aspect of the dashboard (e.g., add a new chart or modify an existing one).
    
- Set up an alert for a specific metric (e.g., CPU usage above 80%).
    

You can learn more about installing and configuring Netdata [here](https://learn.netdata.cloud/docs/agent/packaging/installer).

You can follow the above steps manually to get the understanding of the project. Once you have a working setup, create a few shell scripts to automate the setup and test the monitoring dashboard.

- `setup.sh`: A shell script to install Netdata on a new system.
    
- `test_dashboard.sh`: Script to put some load on the system and test the monitoring dashboard.
    
- `cleanup.sh`: Script to clean up the system and remove the Netdata agent.
    

The goal with this automation is to slowly get accustomed to DevOps practices and CI/CD pipelines.

___
___
## Solution - In Progress

#### Install Netdata on a Linux system & Access the Netdata dashboard through a web browser

Created folder and complete wget install
```bash
cd Documents

mkdir netdata && cd netdata

wget -O /tmp/netdata-kickstart.sh https://get.netdata.cloud/kickstart.sh && sh /tmp/netdata-kickstart.sh
```
- Accessed dashboard using http://localhost:19999 

**Side note:** no need to create folder for installation unless wanting to install config files elsewhere

___
#### Configure Netdata to monitor basic system metrics such as CPU, memory usage, and disk I/O

https://learn.netdata.cloud/docs/collecting-metrics/linux-systems/cpu-performance#setup
- Attempting to edit config file - need to enable Plugin value to yes
```bash
cd /etc/netdata
sudo ./edit-config netdata.conf
```
- Opened in nano
- Navigated down to plugins, it is defaulted to 'yes' value
- Uncommented the value and saved config
- Ran the following commands
```bash
sudo systemctl restart netdata

# Warning: The unit file, source configuration file or drop-ins of netdata.service changed on disk. Run 'systemctl daemon-reload' to reload units.

sudo systemctl daemon-reload
```

___
#### Customize at least one aspect of the dashboard (e.g., add a new chart or modify an existing one)

Created a local custom dashboard and added the following metrics:
- Applications Groups CPU Utilization (app.cpu_utilization)
- Applications Groups memory RSS usage (app.mem_usage)
- Disk I/O bandwitch (disk.io)

___
#### Set up an alert for a specific metric
https://learn.netdata.cloud/docs/alerts-&-notifications/alert-configuration-reference#edit-health-configuration-files

How to:
- Navigate to your Netdata config directory
- Edit an Alert - `sudo ./edit-config health.d/cpu.conf`
- Apply changes - `sudo netdatacli reload-health`

**Created a simple alert - Monitor RAM usage above 80%**
- Already within the correct directory
- Used `sudo ./edit-config health.d/cpu.conf`
- Within config file and using nano - added the below to the bottom of the file
```
# Monitor RAM usage above 80%
 alarm: ram_usage
    on: system.ram
lookup: average -1m percentage of used
 units: %
 every: 1m
  warn: $this > 80
  crit: $this > 90
  info: RAM usage monitoring
```
- Then used `sudo netdatacli reload-health` to apply the changes

___
## Learnings

**Noticed a warning in the alerts section** 
- Instance - `system_post_update_reboot_status`
- Occurred 1800h
- Alert Description - System requires reboot after package updates

- Proceeded to use the following `sudo systemctl restart netdata`
- Didn't removed the warning - this must mean the VM requires a reboot in general
- Proceeded with `sudo shutdown -r now` 
- Alert has now been removed - successful resolution
