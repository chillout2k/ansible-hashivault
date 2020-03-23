# ansible-hashivault
Demo snippets for interaction of ansible against hashicorp vault.

The hashicorp vault instance runs here as a docker deployment in [development mode](https://hub.docker.com/_/vault) at `http://localhost:8200`. It also does not make use of TLS for simplicity in term of a quick and dirty test-setup!

**Do not run the vault server in development mode in production!**

# Hashicorp Vault setup
* runs in development mode!
* authenticates users/clients against a ldap directory
* uses Galera-DB cluster as HA-aware storage backend

# local ansible inventory
```
---
all:
  hosts:
    localhost:
```

# playbook examples
```
---
- hosts: "all"
  remote_user: root
  gather_facts: no

  tasks:
  - name: 'Fetch secrets using "hashi_vault" lookup by token and/or ldap authentication'
    set_fact:
      secret2: "{{lookup('hashi_vault', 'secret=devops/data/secret2:data auth_method=ldap username=' + hashi_ldap_user + ' password=' + hashi_ldap_pass + ' url=http://localhost:8200')}}"
      spielwiese: "{{lookup('hashi_vault', 'secret=devops/data/spielwiese:data token=' + hashi_token + ' url=http://localhost:8200')}}"
      bundle: "{{lookup('hashi_vault', 'secret=devops/data/bundle/mailin/kv:data token=' + hashi_token + ' url=http://localhost:8200')}}"

  - name: 'Debug: print secret2-secret from facts_cache'
    debug:
      msg: "Secret2: {{ secret2['blah'] }}"

  - name: 'Debug: print spielwiese-secret from facts_cache'
    debug:
      msg: "Spielwiese: username={{ spielwiese['username'] }}, password={{ spielwiese['password'] }}"

  - name: 'Debug: print bundle-secret from facts_cache'
    debug:
      msg: "Bundle-Mailin secretskey: {{ bundle['secretskey'] }}"
```
