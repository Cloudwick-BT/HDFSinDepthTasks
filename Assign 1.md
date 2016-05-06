## Viewing File Blocks

####1. Location of block details
  
  `[root@bmaster2 ~]# sudo -u hdfs hdfs fsck /weather -files -blocks -locations`

####2. Location of Namenode directory

  * We can find the namnode directory location in hdfs-site.xml file under the property called _dfs.data.dir_
  * fsimage is updated when we reboot the namenode.
  * Edits are being stored in journal node directory.

  * Shell script to calculate average time of edit log creation. This script will give time in seconds when run in folder where edit log is stored.
'''sh
#!/bin/bash
current=0
total=0
for i in $(ls -tr | grep edits)
do
  if [ "$current" -ne "0" ]; then
   current=$(stat -c%Y $i)
   diff=$((current-last))
   total=$((total+diff))
   last=$current
  else
   current=$(stat -c%Y $i)
   last=$(stat -c%Y $i)
  fi
done

number=$(($(ls | grep edits | wc -l)-1))
echo $((total/number))
'''


## Hadoop file access management

####1. Create user
  `useradd userA`

####2. Create directory
  * To create directory: `hdfs dfs -mkdir userA_dir`
  * To change ownership and permission: 
    `[root@bmaster2 ~]# sudo -u hdfs hdfs dfs -chown userA userA_dir`
    `[root@bmaster2 ~]# sudo -u hdfs hdfs dfs -chmod 700 userA_dir`

####3. Create user B and check if he can access the directory
  `[root@bmaster2 ~]# useradd userB`
  `[root@bmaster2 ~]# sudo -u userB hdfs dfs -cat /user/hdfs/userA_dir/*`
   cat: Permission denied: user=userB, access=READ_EXECUTE, inode="/user/hdfs/userA_dir":userA:hdfs:drwx------

####4. Let userB access the files
