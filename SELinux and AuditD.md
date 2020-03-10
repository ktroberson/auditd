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



# SELinux commands

## View the current status
` getenforce `

## Change SELinux mode
` getenforce `

## Permissive
` setenforce 0 `

## Enforceing
` setenforce 1 `
` getenforce `

## SELinux persistent Configuration
`/etc/selinux/config `

## Troubleshooting selinux
`yum install setroubleshoot-server -y`
## helps make reading logs human readable
`sealert`

### example
`seaslert -a /var/log/audit/audit.log`

## LS Command SElinux provides extra details
`la -laZ`

## PS Command with the "Z" flag option set, shows associated context with the process or file
`ps -fauxZ`

## Can't start Auditd when SELinux is enabled
```
1. verify permissions on  /var/log/audit/audit.log
2. verify the permissions of the following files
  THE PERMISSIONS SHOULD BE 640
      /etc/audit/auditd.conf
      /etc/audit/audit.rules
3. If it doesn't resolve the issue, parse /var/log/messages for the relevant error and then use audit2allow to create module and initialize
      grep "/sbin/auditd" /varf/log/messages | audit2allow -M allowaudit
      semodule -i allowaudit
```

## Addiing execeptions to SELinux
`sudo yum install policycoreutils`

## Verify which syslog ports are allowed
`sudo semanage port -l| grep syslog`

## Add anew port policy  
`sudo semanage port -m -t syslogd_port_t -p tcp 5140`

## May need to authorize the /var/spool/rsyslog directory
`sudo semanage fcontext -a -t syslogd_var_lib_t "/var/spool/rsyslog/*"`
`sudo restorecon -R -v /var/spool/rsyslog`

## May also need to authorize /etc/rsyslog.d/*
```
sudo semanage fcontext -a -t syslog_conf_t "/etc/rsyslog.d/"
sudo restorecon -R -v /etc/rsyslog.d/
sudo semanage fcontext -a -t etc_t "/etc/rsyslog.d"
sudo restorecon -v /etc/rsyslog.d
```

## Restart syslog
`sudo service syslog restart`
