# Active Directory on Ubuntu > 18.04
This project helps with active directory on ubuntu greater or equal than 18.04 but tested only on 22.04 LTS

### Some considerations
- This fast guide does not trying to be verboser explaining all configs details and their implications
- I ingressed on AD domain during instalation of ubuntu 22.04. In deed I dont know if this influence on rest of this guide in
some configurations values for example.
- Use (Oficial White Paper) on pdf folder writen by Canonical

### Install packaged needed

```sh
sudo apt -y install realmd sssd sssd-tools libnss-sss libpam-sss adcli samba-common-bin oddjob oddjob-mkhomedir packagekit
```

### Enable `make homedir` on new user login

```sh
sudo pam-auth-update --enable mkhomedir
sudo pam-auth-update
```

### Edit `/etc/sssd/sssd.conf` on this section the entire configuration file is showed (change with your diferences)

The System Security Services Daemon (SSSD) allow authentication on directory-style backends like LDAP, SAMBA4 and Kerberos, for example.

```sh
sudo vim /etc/sssd/sssd.conf

# File content used here (change if you need or know what to do) -> check white paper for some dubts resolutions
# Links if you are getting in troubles

[sssd]
domains = intranet.ufscar.br
config_file_version = 2
services = nss, pam

[domain/intranet.ufscar.br]
default_shell = /bin/bash
krb5_store_password_if_offline = True
cache_credentials = True
krb5_realm = INTRANET.UFSCAR.BR
realmd_tags = manages-system joined-with-adcli
id_provider = ad
fallback_homedir = /home/%u@%d
ad_domain = intranet.ufscar.br

# set to False to does not use complete domain <user> istead of <user>@domain.com.br 
use_fully_qualified_names = True

ldap_id_mapping = True
access_provider = ad

# https://www.onooks.com/sssd-tsig-verify-failure-update-failed-refused/
dyndns_update = True 

# https://serverfault.com/questions/872542/debugging-sssd-login-pam-sss-system-error
ad_gpo_access_control = permissive
```

### Restart SSSD (Daemon even you update the configuration) and check status if does not have problems

```sh
sudo systemctl restart sssd
systemctl status sssd
```
`


### References maybe can help you
https://computingforgeeks.com/join-ubuntu-debian-to-active-directory-ad-domain/
https://www.freedesktop.org/software/realmd/docs/realmd-conf.html
https://www.server-world.info/en/note?os=Ubuntu_22.04&p=realmd
https://codymoler.github.io/tutorials/ubuntu-active-directory/
https://rohanc.me/sssd-active-directory-ubuntu/
https://sssd.io/docs/ad/ad-provider.html
https://sssd.io/docs/quick-start.html






