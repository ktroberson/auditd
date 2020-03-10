# How to Setup Rsyslog Client to Send Logs to Rsyslog Server in CentOS 7
## Step 1: Verify Rsyslog Installation
```
rpm -q | grep rsyslog
rsyslogd -v
```
#### If not install, install rsyslog
`yum install rsyslog`
## Step 2: Configure Rsyslog Service as Client
`sudo nano /etc/rsyslog`  
## Step 3: Verify audit and audit-libs packages
`sudo yum list audit audit-libs`
