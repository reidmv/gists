# Upgrade Playbook

## Ensure certificate extensions


### Required Extensions

Ensure that for each role, the role's previous extensions are present AND the extensions listed below (per role).

#### Master

```
---
extension_requests:
  # peadm_role
  1.3.6.1.4.1.34380.1.1.9812: 'puppet/master'
  
  # peadm_availability_group
  1.3.6.1.4.1.34380.1.1.9813: 'A'
```

#### Replica

```
---
extension_requests:
  # peadm_role
  1.3.6.1.4.1.34380.1.1.9812: 'puppet/master'
  
  # peadm_availability_group
  1.3.6.1.4.1.34380.1.1.9813: 'B'
```

#### Compiler Group A

```
---
extension_requests:
  # peadm_role
  1.3.6.1.4.1.34380.1.1.9812: 'puppet/compiler'
  
  # peadm_availability_group
  1.3.6.1.4.1.34380.1.1.9813: 'A'
  
  # For 2019.7 and newer
  pp_auth_role: 'pe_compiler'
```

#### Compiler Group B

```
---
extension_requests:
  # peadm_role
  1.3.6.1.4.1.34380.1.1.9812: 'puppet/compiler'
  
  # peadm_availability_group
  1.3.6.1.4.1.34380.1.1.9813: 'B'
  
  # For 2019.7 and newer
  pp_auth_role: 'pe_compiler'
```

#### PuppetDB Database

```
---
extension_requests:
  # peadm_role
  1.3.6.1.4.1.34380.1.1.9812: 'puppet/puppetdb-database'
  
  # peadm_availability_group
  1.3.6.1.4.1.34380.1.1.9813: 'A'
```

#### PuppetDB Database Replica

```
---
extension_requests:
  # peadm_role
  1.3.6.1.4.1.34380.1.1.9812: 'puppet/puppetdb-database'
  
  # peadm_availability_group
  1.3.6.1.4.1.34380.1.1.9813: 'B'
```

### Cert Update Procedure

1. Edit `/etc/puppetlabs/puppet/csr_attributes.yaml` for all nodes and ensure the proper extension requests are listed
2. For all the nodes except the master:
    1. Remove the ssl directory on the node
    2. Clean the node's certificate on the master
    3. Run `puppet agent -t` on the node to request a new certificate
    4. If necessary, sign the certificate on the master
3. On the master, regenerate the certificate according to [these instructions](https://puppet.com/docs/pe/2018.1/individual_pe_component_cert_regeneration.html#regenerate_puppet_master_certs_split_installs)

## Perform Upgrade

On a Bolt host with the peadm module version 1.x installed, run the `peadm::upgrade` plan.

Here there be dragons, but the idea is it should mostly work.
