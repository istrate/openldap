# OpenLDAP
OpenLDAP - Installation (for CentOS/RedHat) and setup

Assuming that the domain is dev.dept.example.com (for example.com just use dc=example,dc=com)

1) Install OpenLDAP

   yum install -y openldap openldap-clients openldap-servers

2) Start slapd service

   systemctl start slapd
   systemctl enable slapd 

3) Create db.ldif file with below content

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=dev,dc=dept,dc=example,dc=com

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=Manager,dc=dev,dc=dept,dc=example,dc=com

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootPW
olcRootPW: ldappassw

4) Add configurations using:

   ldapmodify -Y EXTERNAL  -H ldapi:/// -f db.ldif

5) Do a LDAP shearch 

ldapsearch -x -b "dc=dev,dc=dept,dc=example,dc=com"

6) Create a base.ldif file

#root node
dn: dc=dev,dc=dept,dc=example,dc=com
objectClass: dcObject
objectClass: organizationalUnit
ou: Dev Department Corp

#messaging node
dn: ou=messaging,dc=dev,dc=dept,dc=example,dc=com
ou: messaging
objectClass: organizationalUnit

#users in the messaging organizational unit
dn: ou=users,ou=messaging,dc=dev,dc=dept,dc=example,dc=com
ou: users
objectClass: organizationalUnit

#groups in the messaging organizational unit
dn: ou=groups,ou=messaging,dc=dev,dc=dept,dc=example,dc=com
ou: groups
objectClass: organizationalUnit

7) Create the base using

 ldapadd -x -W -D "cn=Manager,dc=dev,dc=dept,dc=example,dc=com" -f base.ldif

8) Create users.ldif with below content

# Entry for Group imamsg
dn: cn=imamsg,ou=groups,ou=messaging,dc=dev,dc=dept,dc=example,dc=com
objectclass: groupofnames
cn: imamsg
description: Example group 1
member: cn=user,ou=users,ou=messaging,dc=dev,dc=dept,dc=example,dc=com
member: cn=test,ou=users,ou=messaging,dc=dev,dc=dept,dc=example,dc=com


# Entry for Group iot
dn: cn=iot,ou=groups,ou=messaging,dc=dev,dc=dept,dc=example,dc=com
objectclass: groupofnames
cn: iot
description: Example group 2
member: cn=daniel,ou=people,ou=messaging,dc=dev,dc=dept,dc=example,dc=com

# Create Users: user, test, daniel

# entry for User test
dn: cn=test,ou=users,ou=messaging,dc=dev,dc=dept,dc=example,dc=com
cn: test
sn: test
userPassword: test
objectclass: person

# entry for User user
dn: cn=user,ou=users,ou=messaging,dc=dev,dc=dept,dc=example,dc=com
cn: user
sn: user
userPassword: user
objectclass: person

# entry for User daniel
dn: cn=daniel,ou=users,ou=messaging,dc=dev,dc=dept,dc=example,dc=com
cn: daniel
sn: daniel
userPassword: daniel
objectclass: person

9) Create user using:

ldapadd -x -W -D "cn=Manager,dc=dev,dc=dept,dc=example,dc=com" -f users.ldif
