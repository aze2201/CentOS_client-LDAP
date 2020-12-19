
```console
## INSTALLATION OF PACKAGES

dnf install -y nss-pam-ldapd openssl sssd 
#or 
yum -y install nss-pam-ldapd openssl sssd
```

## BACKUP NECESSARY PACKAGES
```
cp /etc/nslcd.conf /etc/nslcd.conf.orig
cp /etc/nsswitch.conf /etc/nsswitch.conf.orig
cp -r /etc/pam.d /etc/pam.orig
```

## UPDATE NSLCD service
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

## CHECK NSLCD service
```
cat /etc/nslcd.conf
```

## MAKE NSSWITCH to CHECK LDAP (Better change manualy.)
```
sed -i 's/files/ldap files/g' /etc/nsswitch.conf
```
## ENABLE AUTH to CHECK LDAP
```
authconfig --updateall --enableldap --enableldapauth
```

## RESTART nslcd service and make it startup.
```
systemctl enable nslcd
systemctl restart nslcd
systemctl disable sssd
# or
chkconfig nslcd on
service nslcd start
chkconfig sssd off
```
## Channge pam from sss to ldap
```
cd /etc/pam.d
sed -i  's/pam_sss/pam_ldap/g' *
```
