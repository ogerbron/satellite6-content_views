# Content View Management

Satellite6-content_views is an Ansible role to easily manage content views in RedHat Satellite 6.x such as:

* Publishing a new content view version
* Promoting a version to an environment
* Deleting old versions

This role only uses the RedHat Satellite HTTP API.

## Requirements

In order to use this role, you will need:

* Ansible > 2.0
* Satellite 6.x 
* HTTPS access authorization from your Ansible machine to your Satellite
* A Satellite user having the following roles:


## Getting started

Below are a few example playbooks for using this role.

This role expects a satellite user with sufficient privileges.

### Publish a new version

``` yml
---
- hosts: localhost
  connection: local
  vars:
    sat_url: satelliteurl.example.com
    sat_user: satellite_user
    sat_password: satellite_password
    sat_org: satellite_organization
    sat_cv_name: content_view_name
  roles:
    - satellite6-content_views
```

### Publish and promote a content view

Publish a new version of a content view and promote it to environment 'satellite_env'.  

``` yml
---
- hosts: localhost
  connection: local
  vars:
    sat_url: satelliteurl.example.com
    sat_user: satellite_user
    sat_password: satellite_password
    sat_org: satellite_organization
    sat_cv_name: content_view_name
    sat_promote: yes
    sat_env_name: satellite_env
  roles:
    - satellite6-content_views
```

### Only promote a content view

Promote the last version of a content view to environment 'satellite_env'.  

``` yml
---
- hosts: localhost
  connection: local
  vars:
    sat_url: satelliteurl.example.com
    sat_user: satellite_user
    sat_password: satellite_password
    sat_org: satellite_organization
    sat_cv_name: content_view_name
    sat_publish: no
    sat_promote: yes
    sat_env_name: satellite_env
  roles:
    - satellite6-content_views
```

## Role Variables

Here is a list of all the default variables for this role, which are also available in defaults/main.yml.

# defaults file for satellite6-content_views

### Mandatory vars ###
#Satellite hostname, can be an IP or DNS
sat_hostname: ""
#Satellite username
sat_user: ""
#Satellite password for username
sat_password: ""
#Satellite Organization
sat_org: ""

#Content view name we want to publish
sat_cv_name: ""
### /Mandatory vars ###

#If set to yes, this will publish a new version
#before anything else
sat_publish: yes

#Content view removal
sat_remove_old_cv: no
#Number of old content views to keep
sat_keep_old_cv: 5

#If set to yes, this will promote the content view
#to the specified env (with var sat_env_name)
sat_promote: no
sat_follow_env_path: yes
#Env to promote to
sat_env_name: "Prod"

