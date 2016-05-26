# AIDE

An Ansible role to install, configure, and schedule AIDE.

The default settings will deploy all the same configuration options that come with a default aide.conf after installing the tool.


## Role Variables
`aide_pkg`: aide                                         #override with a specific version if required   
`aide_dbdir`: /var/lib/aide   
`aide_database_filename`: 'aide.db.gz'                    #only the filename, no path   
`aide_database_out_filename`: 'aide.db.new.gz'            #only the filename, no path   
`aide_gzip_dbout`: 'yes'                                  #yes|no   
`aide_verbose`: 5                                         #level of verbosity 0-255   
`aide_report_url`:   
  - 'file:@@{LOGDIR}/aide.log'   
  - stdout   
`aide_acl_no_symlink_follow`: 'yes'                       #yes|true|no|false   
`aide_warn_dead_symlinks`: 'no'                           #yes|true|no|false   
`aide_grouped`: 'yes'                                     #yes|true|no|false   
`aide_summarize_changes`: 'no'                            #yes|true|no|false   
`aide_report_attributes`: []                             #list of default rules to report: p,i,n,u,g,s,b,m,a,c,S,acl,selinux,xattrs,md5,sha1,sha256,sha512,rmd160,tiger,haval,gost,crc32,whirlpool   
`aide_ignore_list`: []                                   #list of default rules to ignore in reports ^   
`aide_config_version`: 1                                  #optional for information only   
`aide_cron_email_notify`: ''           #Who to email aide report to after aide --check. Can be comma-separated list   
`aide_cronjob_name`: 'aide scheduled database checkup'    #will put a comment in crontab for this scheduled job   
`aide_cron_sched_min`: '0'                                #minute   
`aide_cron_sched_hr`: '1'                                 #hour   
`aide_cron_sched_day`: '*'                                #day   
`aide_cron_sched_mon`: '*'                                #month   
`aide_cron_sched_wkd`: '*'                                #weekday   

## Defining and Undefining aide.conf Variables
    `aide_macros`:   
      `define`:   
         - `name`: Give it a name   
           `variable`: Name_of_Variable   
           `value`: Value of the variable   
         - `name`: DBDIR var   
           `variable`: DBDIR   
           `value`: /var/lib/aide   
      `undefine`:   
         - `name`: Some var to undefine   
           `variable`: Name_of_Variable  #This would effectively undefine the variable we defined above   
         - `name`: Undefining DBDIR var   
           `variable`: DBDIR   


## Defining Rules/Groups, Selection paths, and Ignore/Negative Selection Paths

A YAML spec was built to handle all of these items in a relatively organized way.   

### Attributes available to a rule:

    `aide_rules`:   
      - `name`: My first rule                                                #Required   
        `rule`: FIPSR                                                        #Required   
        `comment`: Comment to put above this rule declaration                #Optional   
        `attributes`: []  #List made up of default rules or defined rules    #Required except on special negative rule   
        `paths`:                                                             #Optional   
           - /my/include/path/1  #Cannot start with '!' see Ignore/Negative Selection Paths   
           - /my/include/path/2


### A Special Rule to handle Ignore/Negative Selection Paths is available

Add a rule to your `aide_rules`: definition with `rule`: negative   
Here's an example, and you can also find an example in this Role's defaults/main.yml:   

    `aide_rules`:   
      - `name`: My negative/ignore selections                                #Required   
        `rule`: negative                                                     #Required   
        `paths`:                                                             #Required   
           - /my/ignore/path/1    
           - /my/ignore/path/2

Do not include an '!' in front of the paths, the template logic will automatically do this for you.   



Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - ahuffman.aide

License
-------

[MIT](LICENSE)

Author Information
------------------
[Andrew J. Huffman](https://github.com/ahuffman)
