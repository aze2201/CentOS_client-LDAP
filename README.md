

Installation of packages
```
dnf install -y nss-pam-ldapd openssl sssd 
#or 
yum -y install nss-pam-ldapd openssl sssd
```

Backup necessary packages
```
cp /etc/nslcd.conf /etc/nslcd.conf.orig
cp /etc/nsswitch.conf /etc/nsswitch.conf.orig
cp -r /etc/pam.d /etc/pam.orig
```

Update NSLCD service
```
cat > /etc/nslcd.conf <<EOF
uid nslcd
gid ldap
uri ldap://192.168.1.85:389
base ou=Staff,ou=Users,dc=ops,dc=incuda,dc=com
tls_reqcert never
filter passwd (objectClass=top)
EOF
```

Check NSLCD service
```
cat /etc/nslcd.conf
```

Update NSSSWITCH to auth with LDAP first (Better change manualy.)
```
sed -i 's/files/ldap files/g' /etc/nsswitch.conf
```
Update system config to  AUTH with LDAP
```
authconfig --updateall --enableldap --enableldapauth
```
Restart NSLCD service and make it startup.
```
systemctl enable nslcd
systemctl restart nslcd
systemctl disable sssd
# or
chkconfig nslcd on
service nslcd start
chkconfig sssd off
```
Channge PAM from SSS to LDAP
```
cd /etc/pam.d
sed -i  's/pam_sss/pam_ldap/g' *
```
