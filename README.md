# Active Directory on Ubuntu 22.04 LTS
This project helps you  configuring active directory (AD) on ubuntu greater than 18.04 but tested only on 22.04 LTS


### Some considerations

- This guide does not trying to be verboser explaining all configs details and their implications

- I ingressed on AD domain during instalation of ubuntu 22.04. In deed I dont know if this influence on rest of this guide in
some configurations values for example.

- Check Oficial White-paper on this root folder writen by Canonical for more specific details

- Depending your AD configuration some configs may be changed (or maybe does not work)

```sh
# Change: 
#
# >>> MyDomain.Com.Br  -- with your AD/SAMBA4 backend directory domain.
#
# -> Some links if you are getting in troubles with your RAW file
# -> Check Canonical White-paper for dubts about configs (pdf folder)
```

### Install needed packages (for ubuntu)

```sh
sudo apt -y install realmd sssd sssd-tools libnss-sss libpam-sss adcli samba-common-bin oddjob oddjob-mkhomedir packagekit
```

### Enable `make homedir` on new user login

```sh
sudo pam-auth-update --enable mkhomedir
sudo pam-auth-update
```

### Join the AD domain
You need join in the domain with admin user of domain (permissions needed) if you doest not did this during installtion of Ubuntu 22.04 LTS

```sh
sudo realm discover MyDomain.Com.Br
sudo realm join -v -U <admin_user_of_domain> MyDomain.Com.Br
sudo realm list
```

### Set Hostname and check name resolution
```sh
# Get MyComputerHostName with command hostname
hostname

# Set Hostname if you want
hostnamectl set-hostname <MyComputerHostName>.MyDomain.Com.Br
host <MyComputerHostsName>.intranet.ufscar.br

# For check purposes
host MyDomain.Com.Br
```

### Edit `/etc/sssd/sssd.conf` 

The System Security Services Daemon (SSSD) allow authentication on directory-style backends like AD, LDAP, SAMBA4 and Kerberos, for example.

- Nn this section the entire configuration file is provided (change with your diferences or your taste)

```sh 
sudo vim /etc/sssd/sssd.conf

[sssd]
domains = MyDomain.Com.Br
config_file_version = 2
services = nss, pam

[domain/MyDomain.Com.Br]
default_shell = /bin/bash
krb5_store_password_if_offline = True
cache_credentials = True
krb5_realm = MYDOMAIN.COM.BR
realmd_tags = manages-system joined-with-adcli
id_provider = ad
fallback_homedir = /home/%u@%d
ad_domain = MyDomain.Com.Br

# set to False to does not use complete domain <user> istead of <user>@domain.com.br 
use_fully_qualified_names = True

ldap_id_mapping = True
access_provider = ad

# https://www.onooks.com/sssd-tsig-verify-failure-update-failed-refused/
dyndns_update = True 

# https://serverfault.com/questions/872542/debugging-sssd-login-pam-sss-system-error
ad_gpo_access_control = permissive
```

### Restart SSSDaemon (whenever you update the sssd configuration file) 
and check status if does not have problems

```sh
sudo systemctl restart sssd
systemctl status sssd
```

## Some USEFUL COMMANDS!!

```sh
id <ad_user_login>@MyDomain.Com.Br

sudo samba-tool domain info MyDomain.Com.Br
sudo samba-tool group list -U <user> -H ldap://MyDomainUser.ufscar.br
sudo samba-tool user list -U <user> -H ldap://MyDomainUser.ufscar.br
sudo samba-tool computer show <computerhostname> -U <user> -H ldap://MyDomainUser.Com.Br
```


### References maybe can help you with your configuration

https://computingforgeeks.com/join-ubuntu-debian-to-active-directory-ad-domain/

https://www.freedesktop.org/software/realmd/docs/realmd-conf.html

https://www.server-world.info/en/note?os=Ubuntu_22.04&p=realmd

https://codymoler.github.io/tutorials/ubuntu-active-directory/

https://rohanc.me/sssd-active-directory-ubuntu/


### Helpful docs information

https://sssd.io/docs/ad/ad-provider.html

https://sssd.io/docs/quick-start.html






