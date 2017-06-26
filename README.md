# OpenShift LDAP Group Sync Role
This Ansible role configures OpenShift LDAP group syncing.

## Requirements

One of:

* OpenShift Enterprise 3.2
* OpenShift Container Platform 3.3 or later
* OpenShift Origin M5 1.3 or later.

## Role Variables

| Name      | Default value   | Description                                                                                                                                        |
|-----------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| config    | None (Required) | Dictionary containing [LDAPSyncConfig](https://docs.openshift.com/container-platform/latest/install_config/syncing_groups_with_ldap.html) resource |
| whitelist | []              | Only synchronize LDAP groups contained in this list. If empty all groups are synced. Can be combined with `blacklist`.                             |
| blacklist | []              | Don't synchronize LDAP groups contained in this list. Can be combined with `whitelist`.                                                            |
| policy    | []              | List of dictionaries containing group role bindings                                                                                                |

## Dependencies

* <https://github.com/appuio/ansible-module-openshift>

## Example Usage

`playbook.yml`:

```yaml
roles:
- role: ansible-role-openshift-ldapsync
  config:
    kind: LDAPSyncConfig
    apiVersion: v1
    url: ldap://LDAP_SERVICE_IP:389 
    insecure: false 
    rfc2307:
      groupsQuery:
        baseDN: "ou=groups,dc=example,dc=com"
        scope: sub
        derefAliases: never
        pageSize: 0
      groupUIDAttribute: dn 
      groupNameAttributes: [ cn ] 
      groupMembershipAttributes: [ member ] 
      usersQuery:
        baseDN: "ou=users,dc=example,dc=com"
        scope: sub
        derefAliases: never
        pageSize: 0
      userUIDAttribute: dn 
      userNameAttributes: [ mail ] 
      tolerateMemberNotFoundErrors: false
      tolerateMemberOutOfScopeErrors: false
  whitelist: [ myldapgroup, myotherldapgroup ]
  policy:
  - cluster_roles: [ cluster-admin ]
    groups: [ myldapgroup, myotherldapgroup ]
```

The concrete structure and values of `config` depend on the LDAP server product and configuration. See https://docs.openshift.com/container-platform/latest/install_config/syncing_groups_with_ldap.html for details.
