#########################
## About This Playbook ##
#########################

This playbook  automates firmware updation process for HP servers by using HP Smart Update Manager (SUM) utility and software repositories using HPSPP ( HP Service Pack for Proliant )

##################
## Prerequisite ##
##################

* This playbook shoud be run from a system that can act as 'admin' node to HP machines whose FW needs to be upgraded
* You need to run these playbooks as ROOT user.
* You would require ROOT passwords of HP machies for which you are going to upgrade FW

###########
## Usage ##
###########

**Step 1 :** Download HP Service Pack for Proliant ISO file from the below URL
http://h17007.www1.hp.com/us/en/enterprise/servers/products/service_pack/spp/index.aspx
To download this you would require valid HP Passport account that have rights to download software support services from HP

**Step 2 :** Once you have downloaded HPSPP ISO, place it under automated_HP_Firmware_upgradation repository with file name as 'hpsum.iso' 

**Step 3 :** Update inventory_file with all the host names that need to be upgraded. 

**Step 4 :** Execute setup.yml playbook to begin F/W upgradation. 

```sh
# ansible-playbook -i inventory_file setup.yml
```
**Step 5 :** If you have not added your host to group '[host_to_reboot]', then you need to manuall reboot your hosts inorder to get new F/W into effect.

**Step 6 (optional) :** You you wish to generate system reports
```sh
# ansible-playbook -i inventory_file generate_report.yml
```
########################
## Miscellaneous Info ##
########################

* All the activity logs will get stored at /var/log/hp

* While ansible playbook is running you can monitor 'admin' and 'HP node' logs using the below command.These logs will tell you what is going on in background.
```sh
# tail -100f /var/hp/log/*.raw
```


#######################
## Where to get Help ##
#######################

If you need any help with this playbook , feel free to contact karan.singh@csc.fi

##################################################
## HPSUM commands that will make your life EASY ##
##################################################

* Assume you have mounted HPSPP ISO under /mnt/HPSUM
* These commands should be executed in the same order to get F/W upgraded. 
```sh 
[Secret] These commands with the exact same sequence has been implemented in this playbook
```
```sh
/mnt/HPSUM/hp/swpackages/hpsum --open_firewall getnodes
/mnt/HPSUM/hp/swpackages/hpsum --open_firewall getbaselines

/mnt/HPSUM/hp/swpackages/hpsum --open_firewall add --nodes $hostname user=root password=$rootpassword type=Linux
/mnt/HPSUM/hp/swpackages/hpsum --open_firewall inventory --nodes $hostname --baselines /mnt/HPSUM/hp/swpackages


/mnt/HPSUM/hp/swpackages/hpsum shutdownengine
/mnt/HPSUM/hp/swpackages/hpsum --open_firewall -s report -target $hostname -targettype Linux -veryv -username root -password $rootpassword


/mnt/HPSUM/hp/swpackages/hpsum shutdownengine
/mnt/HPSUM/hp/swpackages/hpsum --open_firewall -s -firmware_report -target $hostname -targettype Linux -veryv -username root -password $rootpassword 


/mnt/HPSUM/hp/swpackages/hpsum shutdownengine
/mnt/HPSUM/hp/swpackages/hpsum --open_firewall --use_latest -s -f:rom -target $hostname -targettype Linux -veryv -username root -password $rootpassword 
```

