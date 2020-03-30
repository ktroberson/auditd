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
`ls -laZ /etc/audit`  


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

## SELinux Gui    
`sudo yum install policycoreutils-gui.x86_64`  

## Verify which syslog ports are allowed
`sudo semanage port -l| grep syslog`

## Add a new port policy  
`sudo semanage port -m -t syslogd_port_t -p tcp 5140`  
`sudo semanage port -a -t syslogd_port_t -p udp 5140`

## May need to authorize the /var/spool/rsyslog directory
`sudo semanage fcontext -a -t syslogd_var_lib_t "/var/spool/rsyslog/*"`  

## Set the context type with restorecon cmd    
`sudo restorecon -Rv /var/spool/rsyslog`  
`sudo restorecon -Rv /etc/audit`  
`sudo restorecon -Rv /var/log/audit`  

## May also need to authorize /etc/rsyslog.d/*
```
sudo semanage fcontext -a -t syslog_conf_t "/etc/rsyslog.d/"
sudo restorecon -R -v /etc/rsyslog.d/
sudo semanage fcontext -a -t etc_t "/etc/rsyslog.d"
sudo restorecon -v /etc/rsyslog.d
```  

##  How to create the new label
`chcon -t <etc_T> -u <system_u> <filename>`    


## auditd_t process type permissive  
`semanage permissive -a auditd_t`  

## Start auditd service  
`service auditd start`  

## Set persistence   
`systemctl enable auditd`  

## Restart syslog
`sudo service rsyslog restart` 

## Check rules (make sure they are valid)  
`augenrules --check`  
`augenrules --load`  
#### Make all changes to rules in the rules.d 

## Troubleshooting  
`ausearch -c 'in:imfile' --raw | audit2allow -M my-inimfile`  
`semodule -i my-inimfile.pp`  

#### Forwarding audit logs via rsyslog with selinux active  
Do the following if you are unable to get rsyslog to ship auditd logs with SELinux enabled  

Create a module file
`sudo nano rsyslog.te`   
Cut and paste the following
```
module rsyslog 1.0;

require {
        type syslogd_t;
        type auditd_log_t;
        class file read;
        class file ioctl;
        class file open;
        class file getattr;
        class dir search;
}

#============= syslogd_t ==============
allow syslogd_t auditd_log_t:file read;
allow syslogd_t auditd_log_t:file ioctl;
allow syslogd_t auditd_log_t:file open;
allow syslogd_t auditd_log_t:file getattr;
allow syslogd_t auditd_log_t:dir search;
```  
Compile with the following commands  
`checkmodule -M -m -o rsyslog.mod rsyslog.te`  
`semodule_package -o rsyslog.pp -m rsyslog.mod`  
Import the new module
`semodule -i rsyslog.pp`  


## For more information please the visit
`https://www.systutorials.com/docs/linux/man/8-auditd_selinux/`  
