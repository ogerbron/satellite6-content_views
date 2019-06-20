[ ![Image](https://cloud.githubusercontent.com/assets/5514990/24834935/e0d1db04-1d1c-11e7-8ad0-53fd45ff13c3.png "Ansible") ](https://www.ansible.com/ "Ansible")

[![Build Status](https://travis-ci.org/ogerbron/satellite6-content_views.svg?branch=master)](https://travis-ci.org/ogerbron/satellite6-content_views/)
[![Ansible Role](https://img.shields.io/ansible/role/21653.svg)](https://galaxy.ansible.com/ogerbron/satellite6-content_views/)

**Important**: As I moved to another job, I do not use Red Hat Satellite any more, thus I cannot maintain this role easily (I don't have a licence to use Satellite). But feel free to submit PR to improve the role, or use your own repo. Hope you will enjoy it!

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

Sometimes it can be beneficial to automatically update specific content views, for example the base operating system content view.
Satellite can schedule syncs of the repositories, but the synced content will not be available to the clients until the administrator publishes a new version of a content view containing these repositories.

**WARNING**: Regarding composite content views, this will update inner composite views to their latest version before publishing.

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

Publish a new version of a content view and promote it to environment `satellite_env`.
After a new version of a content view is created it is only available in the Library Lifecycle Environment. 
To make it available to other environments, a promotion of the version has to be done. 

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

By default, the promoting will follow the environment path, and promote the content view to every environment until reaching the one specified with `sat_env_name`.

For example, given the following environment path `Library -> Dev -> Testing -> Production` in the above example, setting `sat_env_name: Production` will do:
* Publish new version to Library
* Promote new version to Dev environement
* Promote new version to Testing environment 
* Promote new version to Production environment

### Only promote a content view

Promote the last version of a content view to environment `satellite_env`.  

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
### Remove old content views

While working with Satellite 6, new Content View versions get created pretty often, especially when testing Puppet modules and having to make them available to the clients.
Old versions of the Content Views are kept by Satellite by default to be able to roll back to that specific version in the future. 
But keeping too many old versions makes the Satellite UI unclear and the background tasks slower as they tend to analyse more data.

By default all Content View versions that are promoted to any Lifecycle Environment and the 5 newest non-promoted versions are kept.
The number of kept versions can be adjusted using variable `sat_keep_old_cv`.

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
    sat_remove_old_cv: yes
    sat_keep_old_cv: 5
  roles:
    - satellite6-content_views
```

## Role Variables

Here is a list of all the default variables for this role, which are also available in defaults/main.yml.

| Variable name           | Required | Default                           | Choices  | Comments |
|-------------------------|:--------:|:---------------------------------:|:---------|:---------|
|`sat_hostname`           | yes      | None                              | N/A                   |Satellite hostname, can be an IP or DNS|
|`sat_user`               | yes      | None                              | N/A                   |Satellite username, must have admin privileges|
|`sat_password`           | yes      | None                              | N/A                   |Satellite password for username|
|`sat_org`                | yes      | None                              | N/A                   |Satellite Organization in which the content view is|
|`sat_cv_name`            | yes      | None                              | N/A                   |Content view name we want to publish|
|`sat_publish`            | no       | yes                               |yes/no <br/> true/false|If set to yes, this will publish a new version|
|`sat_remove_old_cv`      | no       | no                                |yes/no <br/> true/false|If set to yes, this will remove old content views, use variable `sat_keep_old_cv` to specify the number of content views to keep |
|`sat_keep_old_cv`        | no       | 5                                 | N/A                   |Number of old content views to keep|
|`sat_promote`            | no       | no                                |yes/no <br/> true/false|If set to yes, this will promote the content view to the specified env (with var `sat_env_name`)|
|`sat_follow_env_path`    | no       | yes                               |yes/no <br/> true/false|If set to yes, this will promote to every environments contained in the environment path until reaching `sat_env_name` (see example [Publish and promote](#publish-and-promote-a-content-view)) |
|`sat_env_name`           | no       | None                              | N/A                   |Env to promote to|
|`sat_wait_task_retries`  | no       | 60                                | N/A                   |These vars can be increased when Satellite promotion is long. Since some content views can take quite a long time to be published you can adjust the number of retries and the delay. The default timeout for a task is `sat_wait_task_retries` x `sat_wait_task_delay`. By default, 60 x 30 = 1800 seconds, meaning ansible will wait for 30min for a content view to finish  being published/promoted/deleted.|
|`sat_wait_task_delay`    | no       | 30                                | N/A                   |Delay between two task checks|
|`sat_publish_description`| no       | Automated publishing from Ansible | N/A                   |A message that will appear in the version description |
|`sat_promote_description`| no       | Automated promotion from Ansible  | N/A                   |A message that will appear in the promotion description|

## TODO

* Publish all content views at once
* Promote a specific version of a content view
* Publish a content view only if there are new packages/puppet modules
* Optimise task search with accurate filters
* Add a bunch of tests

## Licence

GPLv3

## Author Information
Olivier Gerbron
