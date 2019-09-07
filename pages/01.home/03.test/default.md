---
title: vault cli usage
admin:
    children_display_order: collection
content:
    items: '- ''@self.children'''
    order:
        by: date
        dir: desc
    limit: '5'
    pagination: '1'
    url_taxonomy_filters: '1'
---


references:

https://learn.hashicorp.com/vault/secrets-management/sm-static-secrets



## Prerequisites

To perform the tasks described in this guide, you need to have a Vault environment. Refer to the [Getting Started](https://learn.hashicorp.com/vault/getting-started/install) guide to install Vault. Make sure that your Vault server has been [initialized and unsealed](https://learn.hashicorp.com/vault/getting-started/deploy).



## Installing Vault

> Download Vault from offical site
> https://www.vaultproject.io/downloads.html



>  For linux 64-bit:
>
> ```bash
> wget https://releases.hashicorp.com/vault/1.2.1/vault_1.2.1_linux_amd64.zip
> unzip vault_1.2.1_linux_amd64.zip -d /usr/local/bin
> echo "export PATH=$PATH:/usr/local/bin" >> ~/.bashrc
> source  ~/.bashrc
> ```



## Test vault

```bash
echo '10.76.2.35 vault-st.pp.akscsz.cn' >> /etc/hosts
export VAULT_ADDR=https://vault-st.pp.akscsz.cn
export VAULT_SKIP_VERIFY="true"
```



## To install completions, run:
```bash
$ vault -autocomplete-install


## login to vault
$ vault login -method ldap username=<your profile username>
$ vault status
```

## list all secrets engine
```bash
$ vault secrets list
Path          Type         Accessor              Description
----          ----         --------              -----------
aks-pp/       kv           kv_d5aaa5cb           n/a
azure/        kv           kv_fc1489ad           n/a
cubbyhole/    cubbyhole    cubbyhole_5d4f36d5    per-token private secret storage
identity/     identity     identity_5b6ee8e3     identity store
kv/           kv           kv_fed01aff           n/a
secret/       kv           kv_1fb210cf           n/a
sys/          system       system_d0ecff28       system endpoints used for control, policy and debugging
```


## create version 2 kv secret
```bash
$ vault secrets enable -path secret2/ -version 2 kv  
Success! Enabled the kv secrets engine at: secret2/

$ vault kv put secret2/a/b/c hello=123
Key              Value

------

created_time     2019-08-13T03:36:26.840569671Z
deletion_time    n/a
destroyed        false
version          1
```



## vault kv list command is used to list all the keys under current path
```bash
$ vault kv list secret2

# Keys
---
a/

$ vault kv list secret2/a/b

# Keys
---
c

$ vault kv list secret2/a/b/c
No value found at secret2/metadata/a/b/c
```





## vault kv get command is used to Retrieves data from the KV store
```bash
$ vault kv get secret2/a/b/c    
====== Metadata ======
Key              Value
------
created_time     2019-08-13T03:36:26.840569671Z
deletion_time    n/a
destroyed        false
version          1

==== Data ====
Key      Value
------
hello    123
```





## create version 1 kv secret
```bash
$ vault secrets enable -path secret1/ -version 1 kv   
Success! Enabled the kv secrets engine at: secret1/

$ vault kv put secret1/a/b/c hello=123
Success! Data written to: secret1/a/b/c
```



## delete one path (if you want to delete path secret1/a, firstly you need delete path a/b/c and path a/b )
```bash
$ vault delete secret1/a/b/c
Success! Data deleted (if it existed) at: secret1/a/b/c
```



### Q: How do I save multiple values at once?

The two examples introduced in this guide only had a single key/value pair. You can pass multiple values in a single command.

```sh
$ vault kv put kv-v1/dev/config/mongodb url=foo.example.com:35533 db_name=users \
 username=admin password=passw0rd
```

Or, read several secrets from a file:

```sh
$ tee mongodb.json <<EOF
{
    "url": "foo.example.com:35533",
    "db_name": "users",
    "username": "admin",
    "password": "pa$$w0rd"
}
EOF

$ vault kv put kv-v1/dev/config/mongodb @mongodb.json
```
