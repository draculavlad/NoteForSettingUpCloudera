
# Tips

## libs your might need to install 


```
yum install cyrus-sasl-plain  cyrus-sasl-devel  cyrus-sasl-gssapi -y
yum install rpcbind -y
```

* for hue node:
```
yum install krb5-devel cyrus-sasl-gssapi cyrus-sasl-deve libxml2-devel libxslt-devel mysql mysql-devel openldap-devel python-devel python-simplejson sqlite-devel -y
```


* for oozie node:
```
wget http://archive.cloudera.com/gplextras/misc/ext-2.2.zip
unzip ext-2.2.zip 
mv ext-2.2 /var/lib/oozie/
```
