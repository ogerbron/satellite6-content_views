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
* A Satellite user having the admin role


## Getting started

Below are a few example playbooks for using this role.

This role expects a satellite user with sufficient privileges.

### Publish a new version

``` yml
---
- hosts: localhost
  connection: local
  vars:
    sat_hostname: satelliteurl.example.com
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
    sat_hostname: satelliteurl.example.com
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
    sat_hostname: satelliteurl.example.com
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

| Variable name           | Required | Default                           | Choices  | Comments |
|:-----------------------:|:--------:|:---------------------------------:|:--------:|:--------:|
|`sat_hostname`           | yes      | None                              |          |Satellite hostname, can be an IP or DNS|
|`sat_user`               | yes      | None                              |          |Satellite username, must have admin privileges|
|`sat_password`           | yes      | None                              |          |Satellite password for username|
|`sat_org`                | yes      | None                              |          |Satellite Organization in which the content view is|
|`sat_cv_name`            | yes      | None                              |          |Content view name we want to publish|
|`sat_publish`            | no       | yes                               |- yes - no|If set to yes, this will publish a new version|
|`sat_remove_old_cv`      | no       | no                                |- yes - no|If set to yes, this will remove old content views, use variable `sat_keep_old_cv` to specify the number of content views to keep |
|`sat_keep_old_cv`        | no       | 5                                 |          |Number of old content views to keep|
|`sat_promote`            | no       | no                                |- yes - no|If set to yes, this will promote the content view to the specified env (with var `sat_env_name`)|
|`sat_follow_env_path`    | no       | yes                               |- yes - no|TODO|
|`sat_env_name`           | no       | None                              |          |Env to promote to|
|`sat_wait_task_retries`  | no       | 60                                |          |These vars can be increased when Satellite promotion is long. Since some content views can take quite a long time to be published you can adjust the number of retries and the delay. The default timeout for a task is `sat_wait_task_retries` * `sat_wait_task_delay`. By default, 60 * 30 = 1800 seconds, meaning ansible will wait for 30min for a content view to finish  being published/promoted/deleted.|
|`sat_wait_task_delay`    | no       | 30                                |          |Delay between two task checks|
|`sat_publish_description`| no       | Automated publishing from Ansible |          |A message that will appear in the version description |
|`sat_promote_description`| no       | Automated promotion from Ansible  |          |A message that will appear in the promotion description|

## Licence

GPLv3

## Author Information
Olivier Gerbron