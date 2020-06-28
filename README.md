# OpenLDAP
OpenLDAP - Installation (for CentOS/RedHat 7.x) and setup

Assuming that the domain is dev.dept.example.com (for example.com just use dc=example,dc=com)

1) Install OpenLDAP

```yum install -y openldap openldap-clients openldap-servers```

2) Start slapd service

```systemctl start slapd```

```systemctl enable slapd ```

3) Create db.ldif file with below content

```
dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=example,dc=com

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=Manager,dc=example,dc=com

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootPW
olcRootPW: ldappassw
```

4) Add configurations using:

 ```ldapmodify -Y EXTERNAL  -H ldapi:/// -f db.ldif```

5) Do a LDAP shearch 

```ldapsearch -x -b "dc=example,dc=com"```

6) Create a base.ldif file

```
#messaging node
dn: ou=messaging,dc=example,dc=com
objectclass: top
objectclass: organizationalUnit
ou: messaging
```

7) Create the base using

 ```ldapadd -x -W -D "cn=Manager,dc=example,dc=com" -f base.ldif```

8) Create users.ldif with below content

```
dn: ou=users,ou=messaging,dc=example,dc=com
objectclass: top
objectclass: organizationalUnit
ou: users
```

9) Create users OU using:

```ldapadd -x -W -D "cn=Manager,dc=example,dc=com" -f users.ldif```

10) Create a user admin in the OU users using admin.ldif

```
dn: uid=admin,ou=users,ou=messaging,dc=example,dc=com
cn: admin
displayname: 
gidnumber: 0
givenname: admin
homedirectory: /home/admin
loginshell: admin
objectclass: posixAccount
objectclass: top
objectclass: inetOrgPerson
sn: admin
uid: admin
uidnumber: 27600
userpassword: admin
```

11) Add user to the ldap db

```ldapadd -x -W -D "cn=Manager,dc=example,dc=com" -f admin.ldif```

12) Create groups OU using groups.ldif

```
dn: ou=groups,ou=messaging,dc=example,dc=com
objectclass: top
objectclass: organizationalUnit
ou: groups
```

13) Add groups OU to the ldap db

```ldapadd -x -W -D "cn=Manager,dc=example,dc=com" -f groups.ldif```

14) Add admins group and users to OU groups using admins.ldif

```
dn: cn=admins,ou=groups,ou=messaging,dc=example,dc=com
cn: admins
member: uid=admin,ou=users,ou=messaging,dc=example,dc=com
objectclass: groupOfNames
```

15) Add admins.ldif using

```ldapadd -x -W -D "cn=Manager,dc=example,dc=com" -f admins.ldif```
