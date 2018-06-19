# ocp_manage_tenant

Creates and deletes OCP projects and add / remove users to/from the projects.

**Note:** This role does not do any sync with LDAP, for that look at [ocp_manage_tenant_ldap_users](../ocp_manage_tenant_ldap_users) role.

## Adding Tenant Projects example

```yaml
- host: localhost
  tasks:
  name: adding tenant projects
  vars:
    ocp_token: 'iejdwjoejdoiedieowmdoewmdomwoie...'
    tenant_name: 'Tenant_A'
    projects:
        - name: 'DEV'
          displayName: 'Development'
          description: 'Development environment'
        - name: 'TEST'
          displayName: 'Functional Test'
          description: 'Functional Test environment'
        - name: 'DEMO'
          displayName: 'Demonstration'
          description: 'Demo environment'
  include_role: 
    name: ocp_manage_tenant
    tasks_from: add_projects
```

## Deleting Tenant Projects example

```yaml
- host: localhost
  name: deleting tenant projects
  vars:
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

## Appendix

### How to create a token for a service account

```bash
# log in as sys admin
$ oc login -u system:admin

# creates a service account
$ oc create sa automator -n openshift-infra

# adds the account to the admin role
$ oc adm policy add-cluster-role-to-user cluster-admin system:serviceaccount:openshift-infra:automator

# gets the value of the token using the name
$ oc serviceaccounts get-token automator -n openshift-infra
```

### How to check access rights

```bash
$ oc get sa automator -o yaml
$ oc policy who-can create ProjectRequest
```