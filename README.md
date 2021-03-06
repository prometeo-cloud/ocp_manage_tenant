# ocp_manage_tenant

Creates and deletes OCP projects and add / remove users to/from the projects.

**Note:** This role does not do any sync with LDAP, for that look at [ocp_manage_tenant_ldap](../ocp_manage_tenant_ldap) role.

<a name="toc"></a>
## Table of Contents 

- [Pre-requisites](#prereqs)
- [How to add projects to a tenant space](#add-projects)
- [How to delete projects from a tenant space](#delete-projects)
- [How to amend project size](#amend-project-size)

<a name="prereqs"></a>
### Pre-requisites 

In order to use this role the following pre-requisites are required:

- Ansible 2.6
- [Openshift Python Client](#openshift-python-client)
- [Have an OCP token](#ocp-token) 

<a name="add_projects"></a>
## How to add projects to a tenant space [[up](#toc)]

To add project to a tenant space run this role with parameters as indicated below.

```yaml
- hosts: localhost
  tasks: 
    - name: "creating tenant projects"
      include_role: 
        name: ocp_manage_tenant
        tasks_from: create_projects
      vars: 
        ocp_token: "<<add a token here>>"
        projects: 
          - name: DEV
            description: "Development environment"
            displayName: Development
            cpu: 2
            memory: 1
          - name: TEST
            description: "Functional Test environment"
            displayName: "Functional Test"
            cpu: 2
            memory: 1
          - name: DEMO
            description: "Demo environment"
            displayName: Demonstration
            cpu: 2
            memory: 1
        tenant_name: Tenant_A
```

**Note**: role bindings for admin and view roles will be creating in OCP mapping to the following directory group names:
- **tenant_name-project_name-admins**: for the OCP admin role.
- **tenant_name-project_name-users**: for the OCP view role.

This role however, does not create the directory groups, this is done by [ocp_manage_tenant_ldap](../ocp_manage_tenant_ldap).

This role, also create hard resource quotas for vCPUs and memory on projects in the tenant.

The playbook [cfg_ocp_manage_tenant](../cfg_ocp_manage_tenant), aggregates the two roles above and should be the default choice for managing tenant spaces.

<a name="delete_projects"></a>
## How to delete projects from a tenant space [[up](#toc)]

```yaml
- hosts: localhost
  tasks: 
    - name: "deleting tenant projects"
      include_role: 
        name: ocp_manage_tenant
        tasks_from: delete_projects
      vars: 
        ocp_token: "<<add a token here>>"
        projects: 
          - name: DEV
            description: "Development environment"
            displayName: Development
          - name: TEST
            description: "Functional Test environment"
            displayName: "Functional Test"
          - name: DEMO
            description: "Demo environment"
            displayName: Demonstration
        tenant_name: Tenant_A
```

<a name="amend-project-size"></a>
## How to amend a project's size 

```yaml


```

<a name="python-client"></a>
### Installing Openshift Python Client

To install the Openshift Python client run the following commands:

```bash
$ sudo pip install --upgrade setuptools
$ sudo pip install openshift
```

<a name="ocp-token"></a>
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
