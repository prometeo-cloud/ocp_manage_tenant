# ocp_manage_tenant

Creates and deletes OCP projects and add / remove users to/from the projects.

**Note:** This role does not do any sync with LDAP, for that look at [ocp_manage_tenant_ldap_users](../ocp_manage_tenant_ldap_users) role.

<a name="toc"></a>
## Table of Contents 

- [How to add projects to a tenant space](#add-projects)
- [How to delete projects from a tenant space](#delete-projects)
- [How to add users to a project](#add-users)
- [How to remove users from a project](#remove-users)
- [How to amend project size](#amend-project-size)

<a name="add_projects"></a>
## How to add projects to a tenant space [[up](#toc)]

```yaml
- hosts: localhost
  tasks:
  - name: adding tenant projects
    vars:
        ocp_token: '<<add a token here>>'
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
        tasks_from: create_projects
```

<a name="delete_projects"></a>
## How to delete projects from a tenant space [[up](#toc)]

```yaml
- hosts: localhost
  tasks:
  - name: adding tenant projects
    vars:
        ocp_token: '<<add a token here>>'
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
        tasks_from: create_projects
```

<a name="add-users"></a>
## How to add users to a project [[up](#toc)]

```yaml
- hosts: localhost
  name: adding tenant project users
  vars:
    ocp_token: '<<add a token here>>'
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
    tasks_from: create_role_bindings
```

<a name="remove-users"></a>
## How to remove users from a project [[up](#toc)]
```yaml
- hosts: localhost
  name: removing tenant project users
  vars:
    ocp_token: '<<add a token here>>'
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
    tasks_from: delete_role_bindings
```
<a name="amend-project-size"></a>
## How to amend a project's size 

```yaml


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