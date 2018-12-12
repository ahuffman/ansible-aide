# ahuffman.aide

## Description
An Ansible role to install, configure, and schedule AIDE.

|**Please Note**|
|---|
|*The default settings will deploy the configuration options that come with a default aide.conf after installing the tool.*|
|*This has only been thoroughly tested on Fedora and RHEL7 Operating Systems.  Please open issues if you have a problem on your platform.*|


## Role Variables
| Variable Name | Description | Required | Default Value | Type |
| :---: | :---: | :---: | --- | :---:|
| aide_pkg | Name of the aide package to install.  Override with a specific version if required. | Yes | "aide" | string |
|aide_conf_path| Path to the aide configuration file | Yes | "/etc/aide.conf"| string |
|aide_update_db| Whether or not to force an update of the aide database on this Role invocation | Yes | False| boolean |

**TODO** - Finish converting documentation format

`aide_dbdir`: "/var/lib/aide"
`aide_logdir`: "/var/log/aide"
`aide_database_filename`: 'aide.db.gz'                    #only the filename, no path   
`aide_database_out_filename`: 'aide.db.new.gz'            #only the filename, no path   
`aide_gzip_dbout`: 'yes'                                  #yes|no   
`aide_verbose`: 5                                         #level of verbosity 0-255   
`aide_report_url`: []  
`aide_acl_no_symlink_follow`: 'yes'                       #yes|true|no|false   
`aide_warn_dead_symlinks`: 'no'                           #yes|true|no|false   
`aide_summarize_changes`: 'no'                            #yes|true|no|false   
`aide_report_attributes`: []                              #list of default rules to report: p,i,n,u,g,s,b,m,a,c,S,acl,selinux,xattrs,md5,sha1,sha256,sha512,rmd160,tiger,haval,gost,crc32,whirlpool   
`aide_ignore_list`: []                                    #list of default rules to ignore in reports ^   
`aide_config_version`: 1                                  #optional for information only   
`aide_cron_schedule_check`: True                          #Whether or not to add a scheduled aide --check to cron you'd have to manually run your checks if preferred
`aide_cron_email_notify`: ''           #Who to email aide report to after aide --check. Can be comma-separated list   
`aide_cronjob_name`: 'aide scheduled database checkup'    #will put a comment in crontab for this scheduled job   
`aide_cron_sched_min`: '0'                                #minute   
`aide_cron_sched_hr`: '1'                                 #hour   
`aide_cron_sched_day`: '*'                                #day   
`aide_cron_sched_mon`: '*'                                #month   
`aide_cron_sched_wkd`: '*'                                #weekday   

## Defining and Undefining aide.conf Variables
```yaml
aide_macros:   
  define:   
     - name: "Give it a name"
       variable: "Name_of_Variable"
       value: "Value of the variable"
     - name: "DBDIR var"
       variable: "DBDIR"
       value: "/var/lib/aide"
  undefine:   
     - name: "Some var to undefine"
       variable: "Name_of_Variable"  #This would effectively undefine the variable we defined above
     - name: "Undefining DBDIR var"
       variable: "DBDIR"
```

## Defining Rules/Groups, Selection paths, and Ignore/Negative Selection Paths

A YAML spec was built to handle all of these items in a relatively organized way.  

### Attributes available to a rule
```yaml
aide_rules:   
  - name: "My first rule"                                                #Required   
    rule: "FIPSR"                                                        #Required   
    comment: "Comment to put above this rule declaration"                #Optional   
    attributes: []  #List made up of default rules or defined rules      #Required except on special negative rule   
    paths:                                                               #Optional   
       - "/my/include/path/1"  #Cannot start with '!' see Ignore/Negative Selection Paths   
       - "/my/include/path/2"
```

### A Special Rule to handle Ignore/Negative Selection Paths is available

Add a rule to your `aide_rules`: definition with `rule`: negative   
Here's an example, and you can also find an example in this Role's defaults/main.yml:   

    aide_rules:   
      - name: My negative/ignore selections                                #Required   
        rule: negative                                                     #Required   
        paths:                                                             #Required   
           - /my/ignore/path/1    
           - /my/ignore/path/2

Do not include an '!' in front of the paths, the template logic will automatically do this for you.  


### Scheduled Cron Aide Checks

The default is to setup an 'aide --check' in crontab.  Should you wish to change this after already allowing this role to create the cron job, simply switch the variable `aide_cron_schedule_check` to False.  This will remove the cron job from your system's crontab on the next playbook run.  One caveat to be aware of is that the `aide_cronjob_name` variable must match what's currently in the crontab to be removed properly.


Example Playbook
----------------


    - hosts: servers
      roles:
         - ahuffman.aide

License
-------

[MIT](LICENSE)

Author Information
------------------
[Andrew J. Huffman](https://github.com/ahuffman)
