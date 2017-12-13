# ocp_manage_tenant

## Description:

A role library to manage OpenShift tenants.  

A **tenant** is a collection of OCP projects (or Kubernetes Namespaces).   
A tenant provides the structure on which to build a Continuous Delivery (CD) pipeline.  

For example, a tenant can be made up of the following OCP projects (environments):  
- **DEV**: 
    - where the automation server is deployed to control the deployment of artefacts across the CD pipeline.
    - where the application source code is integrated, one or more Docker images are produced using Source to Image (S2I) technology in OCP.
    - where automated tests are executed.
    
- **TEST**: 
    - where manual functional testing activities are carried out. 
    
- **DEMO**:
    - where product owners can explore the features of the application before it progresses any further in the CD   pipeline.

Application promotion across environments can be achieved using image tagging.


## Behaviour:

**Feature:** Create Tenant 
- **Given** the name of the application is known.
- **Given** OCP users exists in the Identity Management Server (LDAP).
- **Given** the name of two or more environments is specified.
- **Given** a T-Shirt size for resource quotas used by all environments in the tenant is selected.
- **Given** one or more administrators for each environment (project) in the pipeline are known.
- **Given** one or more basic users for each environment (project) in the pipeline are known.
- **When** the tenant creation is requested.
- **Then** a number of OCP projects are created with a common base name and a different environment suffix.
- **Then** a group for each project / user role is created in the Identity Management Server (LDAP).
- **Then** relevant users are added to the required roles.  
- **Then** Jenkins is added to one of the project to drive automation.

**Feature:** Add Project to Tenant 

**Feature:** Remove Project from Tenant

**Feature:** Add User to Project

**Feature:** Remove User from Project

**Feature:** Change Project Size


## Configuration:

A list of the external variables used by the role.

| Variable  | Description  | Example  | 
|---|---|---|
| **ocp_uri**  | The URI of the OCP server, including port.  |  https://127.0.0.1:8443 |
| **ocp_token**  | The token used to authenticate with the OCP server. It should be for a service account with cluster-admin role.  | "account-token-89si3" |
| **idm_uri**  | The URI of the LDAP server used by OCP to authenticate users. |   |
| **idm_token**  | The token used to connect to the LDAP server. | ldap://ldap_hostname:389/ cn=users,cn=accounts, dc=eu-west-2,dc=compute,dc=internal?uid |
| **tenant_name**  | The name of the tenant to be created. This name is used to name the projects in a CD the pipeline.  | MyApp  |
| **projects**  | A list of projects making up the Tenant. | See usage section - create tenant.  |


## Usage:

How to invoke the role from a playbook:

### Create Tenant

```yaml
- name: Creates Tenant
  include_role:
    name: ocp_manage_tenant
    tasks_from: create_tenant
  vars:
    ocp_uri: 'https://127.0.0.1:8443'
    ocp_token: ''
    idm_uri: ''
    idm_token: ''
    tenant_name: 'Tenant_A'
    projects: 
      - name: 'DEV'
        automation_server: yes
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
          automation_server: yes
          admins:
            - demoAdmin@acme.com
          users:
            - demo1@acme.com
            - demo2@acme.com
```

### How to create a token


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

[OCP API](https://docs.openshift.com/container-platform/3.6/rest_api/openshift_v1.html)
