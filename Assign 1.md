## Viewing File Blocks

####1. Location of block details
  
  * `[root@bmaster2 ~]# sudo -u hdfs hdfs fsck /weather -files -blocks -locations`
  * ![Screenshot of fsck](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/1_block_location.png?raw=true)

####2. Location of Namenode directory

  * We can find the namnode directory location in hdfs-site.xml file under the property called _dfs.data.dir_
  * ![Image of location of namenode directory](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/1.b_nameNode_location.png?raw=true)
  * fsimage is updated when we reboot the namenode.
  * Edits are being stored in journal node directory.

  * Shell script to calculate average time of edit log creation. This script will give time in seconds when run in folder where edit log is stored.
```sh
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
```

  * There were two fsimage, where first one is copy of fsimage before rollback. So the difference between timing of two fsimage is frequency of edits rollback.
  * ![Image of edit rollback](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/1_b_editlog.png?raw=true)


## Hadoop file access management

####1. Create user
  * ![useradd userA](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/2_a_useradd.png?raw=true)

####2. Create directory
   * ![To create directory: `hdfs dfs -mkdir userA_dir`](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/2_b_makedir.png?raw=true)
   * ![To change ownership and permission](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/2_b_changeDirPermissio.png?raw=true) 
    

####3. Create user B and check if he can access the directory
  * `[root@bmaster2 ~]# useradd userB`
  * ![Image of userB being denied](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/2_c_userB.png?raw=true)

####4. Let userB access the files
  * First we will create a group and add that user to the created group and then we will give group permission to the respective file.
  * ![Image of group creation](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/2_d_add_Group.png?raw=true)

####5. Working with ACLs
  * ![Creation of user with respective groups](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/2_e_3users.png?raw=true)
  * Add property to _hdfs-site.xml_
  * ![Add property for ACL](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/2_e_aclproperty.png?raw=true)
  * ![Restart namenode](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/2_e_startnamenode.png?raw=true)
  * ![Give ACL permission](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/2_e_acl.png?raw=true)
  *![Check ACL permission](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/2_e_acl_check.png?raw=true)

####6. Load data with specific replication factor
  * ![loading Data](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/2_6_replicationOnCommndLinepng.png?raw=true)

####7. Change replication factor
  * ![create foofoo.txt](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/2_7_foofooCreated.png?raw=true)
  * ![Change replication factor](https://github.com/Cloudwick-BT/HDFSinDepthTasks/blob/master/screenshots/2_7_foofooReplicatedTo1.png?raw=true)