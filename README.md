<div align="center">
  <h1>Freeradius-server</h1>
  <p>
    <strong>Modified for the <a href="https://www.42.fr/42-network/">42Network</a></strong>
  </p>
  <p>

[![GNU](https://img.shields.io/badge/license-GNU%202-blue.svg)](LICENSE)

  </p>
</div>

Server setup / Dev setup
--------------------------

- Install all deps
```bash
$ apt install git libldap-dev libssl1.0-dev make gcc libsasl2-modules-gssapi-mit ldap-utils libtalloc-dev
# Your working env should looks like :
# - working dir
# |- krb5
# |- freeradius-server
# That's because freeradius will looks for ../../krb5.
```
- Ensure your LDAP server had configured encryption type to support `arcfour-hmac:normal`. If you have adjusted your configuration, the user must change their password again in order for the hash to be created.
- The content of your KDC configuration should look like this (on 42 environment).
```
# /etc/krb5kdc
[kdcdefaults]
    kdc_ports = 750,88

[realms]
    EXAMPLE.TLD = {
        database_name = /var/lib/krb5kdc/principal
        admin_keytab = FILE:/etc/krb5kdc/kadm5.keytab
        acl_file = /etc/krb5kdc/kadm5.acl
        key_stash_file = /etc/krb5kdc/stash
        kdc_ports = 750,88
        max_life = 10h 0m 0s
        max_renewable_life = 7d 0h 0m 0s
        master_key_type = des3-hmac-sha1
        supported_enctypes = aes256-cts:normal arcfour-hmac:normal des3-hmac-sha1:normal des-cbc-crc:normal des-cbc-md5:normal des:normal des:v4 des:norealm des:onlyrealm des:afs3
        default_principal_flags = +preauth
    }
```

Changes
--------------------------
As per GNUv2 we must specify which change we've made to the source code. 
This repo provide an additional modules, named rlm_mschapv2_kerberos which aims to provide compatibility between KRB5 and MSCHAPv2 protocole (based on [Ether42](https://github.com/ether42/freeradius-ldap-kerberos)).

### rlm_mschap_kerberos
Config sample:
```
mschapv2_kerberos {
	#
	# libkdb path for dynamic linking
	#
	libkdb_path = /usr/local/lib/libkdb5.so

	#
	# libkdb_ldap path for dynamic linking
	#
	libkdb_ldap_path = /usr/local/lib/libkdb_ldap.so
   [...]
}
```
