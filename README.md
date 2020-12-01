# CentOS_client-LDAP


## INSTALLATION OF PACKAGES
<pre>
dnf install -y nss-pam-ldapd openssl sssd 
#or 
yum -y install nss-pam-ldapd openssl sssd
</pre>
## BACKUP NECESSARY PACKAGES
<pre>
cp /etc/nslcd.conf /etc/nslcd.conf.orig
cp /etc/nsswitch.conf /etc/nsswitch.conf.orig
cp -r /etc/pam.d /etc/pam.orig
</pre>
## UPDATE NSLCD service 
<pre>
cat > /etc/nslcd.conf <<EOF
uid nslcd
gid ldap
uri ldap://192.168.1.85:389
base ou=Staff,ou=Users,dc=ops,dc=incuda,dc=com
tls_reqcert never
filter passwd (objectClass=top)
EOF
</pre>
## CHECK NSLCD service
<pre>
cat /etc/nslcd.conf
</pre>
## MAKE NSSWITCH to CHECK LDAP
<pre>
sed -i 's/files/ldap files/g' /etc/nsswitch.conf
</pre>
## ENABLE AUTH to CHECK LDAP
<pre>
authconfig --updateall --enableldap --enableldapauth
cd /etc/pam.d
sed -i  's/pam_sss/pam_ldap/g' *
</pre>
## RESTART nslcd service and make it startup.
<pre>
systemctl enable nslcd
systemctl restart nslcd
systemctl disable sssd
# or
chkconfig nslcd on
service nslcd start
chkconfig sssd off 
</pre>

