# ludus-ad-vulns

## Ansible Role: Active Directory Vulnerabilities (Ludus)

An Ansible Role that adds vulnerabilities in an Active Directory.

The role can add the following vulnerabilities:
- Kerberoastable user
- Add ACL to AD objects
- Set unconstrained delegation for a user
- Add open network shares

I plan to add more vulnerabilities to this role, as they become needed on my ranges. 

---

## Requirements

None.

---

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

> [!WARNING]  
> Everything should be configured in the range configuration.

---

### Variables used when configuring a Kerberoastable user:

```yaml
# SAM account name for the user that you will add an SPN to
user.identity: fives

# The Service Principal Name (SPN) value
user.service_principal_name: HTTP/ArcTraining
```

---

### Variables used when configuring a user for unconstrained delegation:

```yaml
# SAM account name for the user that you will add unconstrained delegation to
user.identity: cody
```

---

### Variables used when configuring ACLs for AD objects:

```yaml
# The object you want to assign the ACL for
acl.value.for: "cptrex"

# The object you want to assign the ACL to
acl.value.to: "CN=Bounty Hunters,OU=Bounty Hunters,DC=maldev,DC=local"

# The ACL that you wish to apply
acl.value.right: "GenericAll"

# Whether the ACL should have inheritance (e.g., None, All)
acl.value.inheritance: "None"

#This will give cptrex GenericAll over the Bounty Hunters group
```

---

### Variables used to include vulnerabilities:

```yaml
# Vulnerabilities default to false, allowing you to cherry-pick which vulnerabilities you want to apply to each host

ludus_ad_vulns_openshares: false
ludus_ad_vulns_kerberoasting: false
ludus_ad_vulns_unconstrained_delegation_user: false
ludus_ad_vulns_set_acl: false
```



## Example Ludus Range Config

```yaml
  - vm_name: "{{ range_id }}-ad-dc-win2022-server-x64-1"
    hostname: "{{ range_id }}-DC01"
    template: win2022-server-x64-template
    vlan: 10
    ip_last_octet: 11
    ram_gb: 4
    cpus: 4
    windows:
      sysprep: true
    domain:
      fqdn: maldev.local
      role: primary-dc
    roles:
      - ludus-ad-vulns
    role_vars:
      ludus_ad_vulns_openshares: true
      ludus_ad_vulns_kerberoasting: true
      kerberoasting_users:
      - identity: fives
        service_principal_name: HTTP/ArcTraining

      ludus_ad_vulns_unconstrained_delegation_user: true
      unconstrained_delegation_user: 
      - identity: cody 

      ludus_ad_vulns_set_acl: true
      acl_definitions:
        grant_generic_all_from_cptrex_to_bounty_group:
          for: "cptrex"
          to: "CN=Bounty Hunters,OU=Bounty Hunters,DC=maldev,DC=local"
          right: "GenericAll"
          inheritance: "None"   
```

## License

GPLv3

## Author Information

This role was created by [Primusinterp](https://github.com/Primusinterp), for [Ludus](https://ludus.cloud/).