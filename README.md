# NoteForSettingUpCloudera

## Haredware Condition
  * Two servers of 64G RAM, 1T Storage
  * A lan network with the access to internet
  
## Infrastructure Platform
  * VMware Esxi
  
## Operating System
  * CentOS 7

## Create Machine 

  * for now I created 6 virtual machines:

*Hostname* | *IP* | *OS* | *RAM size* | *Storage size*
------------|------------|------------|------------|------------
mgr.cdh.yu | 10.210.5.60 |centos7 | 8G | 200GB
ag0.cdh.yu | 10.210.5.61 |centos7 | 8G | 200GB
ag1.cdh.yu | 10.210.5.62 |centos7 | 20G | 200GB
ag2.cdh.yu | 10.210.5.63 |centos7 | 20G | 200GB
ag3.cdh.yu | 10.210.5.64 |centos7 | 20G | 200GB
ag4.cdh.yu | 10.210.5.65 |centos7 | 20G | 200GB

## Preparation
  * make sure everything done below on every machine:
  * - update your system
  * - shutdown your firewall 
  * - setup local dns for the cluser
  * - modify your hostanme of every machine in your cluster for the local dns
  * - download jdk and setup env variable for jdk(for my instance, jdk8 is preferred)
  * - make mgr could ssh any machine without login as a sudoer
  * - install ntp service for every machine, make sure thery running at the same timezonge indicating the same time
  * - install a mysql server on mgr.cdh.yuA
  * - create database in mysql for all the service you are going to install on your cluster:

```
create database hive DEFAULT CHARACTER SET utf8;
create database amon DEFAULT CHARACTER SET utf8;
create database cm DEFAULT CHARACTER SET utf8;
create database rman DEFAULT CHARACTER SET utf8;
create database metastore DEFAULT CHARACTER SET utf8;
create database sentry DEFAULT CHARACTER SET utf8;
create database nav DEFAULT CHARACTER SET utf8;
create database navms DEFAULT CHARACTER SET utf8;
create database oozie DEFAULT CHARACTER SET utf8;
```
  
  * - download cloudera-manager installation package on your manager host, for my instance (mgr.cdh.yu)
  
## installation

* extraction

```
tar zxf cloudera-manager-centos7-cm5.7.1_x86_64.tar.gz 
mv cm-5.7.1/ /opt/
mv cloudera/ /opt/
```

* add mysql drive to cdh and initialize the mysql for cdh

```
cp mysql-connector-java-5.1.30.jar cm-5.7.1/share/cmf/lib/
bash /opt/cm-5.7.1/share/cmf/schema/scm_prepare_database.sh mysql -h mgr.cdh.yu -uroot -p1q2w3e --scm-host mgr.cdh.yu scm scm scm
```

* add parcel you would like to install in your parcel repo

```
cp CDH-5.7.1-1.cdh5.7.1.p0.11-el7.parcel /opt/cloudera/parcel-repo/
cp CDH-5.7.1-1.cdh5.7.1.p0.11-el7.parcel.sha /opt/cloudera/parcel-repo/
cp manifest.json /opt/cloudera/parcel-repo/
```
  
* create yousr for cloudera on every machine of your cdh cluster

```
useradd --system --home=/opt/cm-5.7.1/run/cloudera-scm-server/ --no-create-home --shell=/bin/false --comment "Cloudera SCM User" cloudera-scm
```

* config hostname for cdh in file [/opt/cm-5.7.1/etc/cloudera-scm-agent/config.ini] and set server_host=mgr.cdh.yu

* synchronize your cm-5.7.1 dir to very other machine of your cdh cluster

```
scp -r cm-5.7.1/ root@ag1.cdh.yu:/opt/
scp -r cm-5.7.1/ root@ag2.cdh.yu:/opt/
scp -r cm-5.7.1/ root@ag3.cdh.yu:/opt/
scp -r cm-5.7.1/ root@ag4.cdh.yu:/opt/
scp -r cm-5.7.1/ root@ag5.cdh.yu:/opt/
```

* launch service at system start

* - on manager node:

```shell 
echo "/opt/cm-5.7.1/etc/init.d/cloudera-scm-server start" >> /etc/rc.local
```

* - on agent node:

```
echo "/opt/cm-5.7.1/etc/init.d/cloudera-scm-agent start" >> /etc/rc.local
```

* add mysql drive into hive lib on nodes which you would install hive
```
cp /opt/cm-5.7.1/share/cmf/lib/mysql-connector-java-5.1.30.jar /opt/cloudera/parcels/CDH-5.7.1-1.cdh5.7.1.p0.11/lib/hive/lib/
```

* add mysql drive into global share lib on every machine of your cdh cluster
```
cp /opt/cm-5.7.1/share/cmf/lib/mysql-connector-java-5.1.30.jar /usr/share/java/mysql-connector-java.jar
```


## start cluster

for manager node:

```
/opt/cm-5.7.1/etc/init.d/cloudera-scm-server restart
```

for agent node:

```
/opt/cm-5.7.1/etc/init.d/cloudera-scm-agent restart
```
