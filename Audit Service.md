## Starting the Audit Service 
`service auditd start`  

#### Optionally, you can configure auditd to start at boot time using the following command as the root user:  
`chkconfig auditd on`   

#### A number of other actions can be performed on auditd using the service auditd action command, where action can be one of the following:  
```  
stop — stops auditd.
restart — restarts auditd.
reload or force-reload — reloads the configuration of auditd from the /etc/audit/auditd.conf file.
rotate — rotates the log files in the /var/log/audit/ directory.
resume — resumes logging of Audit events after it has been previously suspended, for example, when there is not enough free space on the disk partition that holds the Audit log files.
condrestart or try-restart — restarts auditd only if it is already running.
status — displays the running status of auditd.  
```  

## Defining File System Rules  
#### where:  
~~~  
path_to_file is the file or directory that is audited.
permissions are the permissions that are logged:
   r — read access to a file or a directory.
   w — write access to a file or a directory.
   x — execute access to a file or a directory.
   a — change in the file's or directory's attribute. 
   key_name is an optional string that helps you identify which rule or a set of rules generated a particular log entry.  
     
