# ocp_manage_tenant

Creates and deletes OCP projects and add / remove users to/from the projects.

**Note:** This role does not do any sync with LDAP, for that look at [ocp_manage_tenant_ldap_users](../ocp_manage_tenant_ldap_users) role.

## Adding Tenant Projects example

```yaml
- host: localhost
  name: adding tenant projects
  vars:
    ocp_uri: 'https://<<ip>>:8443'
    ocp_token: 'iejdwjoejdoiedieowmdoewmdomwoie...'
    tenant_name: 'Tenant_A'
    projects:
        - name: 'DEV'
        - name: 'TEST'
        - name: 'DEMO'
  include_role: 
    name: ocp_manage_tenant
    tasks_from: add_projects
```

## Deleting Tenant Projects example

```yaml
- host: localhost
  name: deleting tenant projects
  vars:
    ocp_uri: 'https://<<ip>>:8443'
    ocp_token: 'iejdwjoejdoiedieowmdoewmdomwoie...'
    tenant_name: 'Tenant_A'
    projects:
        - name: 'DEV'
        - name: 'TEST'
        - name: 'DEMO'
  include_role: 
    name: ocp_manage_tenant
    tasks_from: delete_projects
```


## Adding Tenant Project users example

```yaml
- host: localhost
  name: adding tenant project users
  vars:
    ocp_uri: 'https://<<ip>>:8443'
    ocp_token: 'iejdwjoejdoiedieowmdoewmdomwoie...'
    tenant_name: 'Tenant_A'
    projects:
      - name: 'DEV'
        admins:
          - devAdmin@acme.com
        users:
          - dev1@acme.com
          - dev2@acme.com
    - name: 'TEST'
      admins:
        - testAdmin@acme.com
      users:
        - tester1@acme.com
        - tester2@acme.com
    - name: 'DEMO'
      admins:
        - demoAdmin@acme.com
      users:
        - demo1@acme.com
        - demo2@acme.com
  include_role: 
    name: ocp_manage_tenant
    tasks_from: add_users
```

```yaml
- host: localhost
  name: removing tenant project users
  vars:
    ocp_uri: 'https://<<ip>>:8443'
    ocp_token: 'iejdwjoejdoiedieowmdoewmdomwoie...'
    tenant_name: 'Tenant_A'
    projects:
      - name: 'DEV'
        displayName: 'Development'
        description: 'Development environment'
        admins:
          - devAdmin@acme.com
        users:
          - dev1@acme.com
          - dev2@acme.com
    - name: 'TEST'
      displayName: 'Functional Test'
      description: 'Functional test environment'
      admins:
        - testAdmin@acme.com\\
      users:
        - tester1@acme.com
        - tester2@acme.com
    - name: 'DEMO'
      displayName: 'Demonstration'
      description: 'Demonstration environment'
      admins:
        - demoAdmin@acme.com
      users:
        - demo1@acme.com
        - demo2@acme.com
  include_role: 
    name: ocp_manage_tenant
    tasks_from: add_users
```