[![Build Status](https://travis-ci.org/tumf/ansible-role-systemd-service.svg)](https://travis-ci.org/tumf/ansible-role-systemd-service)
[![Ansible Galaxy](http://img.shields.io/badge/ansible--galaxy-systemd--service-blue.svg)](https://galaxy.ansible.com/tumf/systemd-service/)

systemd-service - Ansible role
===============

Register services to systemd.

> **Japanese ver**
> http://qiita.com/tumf/items/9d5ac6853685ba53214d


Role Variables
--------------

All systemd services are defined as list of dictionaries under `systemd_service_units`.
Below are the relevant fields required.


|name                |type    |default|description
|--------------------|--------|-------|-------------
|`default_dir`|String|"/etc/default"|envs file path
|`systemd_dir`|String|"/etc/systemd/system"|systemd path
|`name` * |String||service name
|`envs`|Dict|{}|envs (/etc/default/:name)
|`envs_raw`|List|[]|envs (/etc/default/:name)

> **Note**
> `root_dir` is obsolate.


### [Unit]


|name                |type    |default|description
|--------------------|--------|-------|-------------
|`Unit_Description`|String||[Unit]Description
|`Unit_Documentation`|String||[Unit]Documentation
|`Unit_Requires`|String,List||[Unit]Requires
|`Unit_Wants`|String,List||[Unit]Wants
|`Unit_ConditionPathExists`|String||[Unit]ConditionPathExists
|`Unit_After`|String,List||[Unit]After
|`Unit_Before`|String,List||[Unit]Before


### [Service]


|name                |type    |default|description
|--------------------|--------|-------|-------------
|`Service_Type`|String|"simple"|[Service]Type
|`Service_ExecStartPre`|String,List||[Service]ExecStartPre
|`Service_ExecStart` * |String||[Service]ExecStart
|`Service_ExecStartPost`|String,List||[Service]ExecStartPost
|`Service_Restart`|String|"no"| [Service]Restart "no" or "always" or "on-success" or "on-failure"
|`Service_RestartSec`|Integer|| [Service]RestartSec
|`Service_ExecReload`|String|| [Service]ExecReload
|`Service_ExecStop`|String|| [Service]ExecStop
|`Service_KillMode`|String|| [Service]KillMode
|`Service_ExecStopPost`|String,List|| [Service]ExecStopPost
|`Service_PIDFile`|String|| [Service]PIDFile
|`Service_BusName`|String|| [Service]BusName
|`Service_PrivateTmp`|String|| [Service]PrivateTmp
|`Service_LimitNOFILE`|String|| [Service]LimitNOFILE
|`Service_User`|String|| [Service]User
|`Service_Group`|String|| [Service]Group
|`Service_WorkingDirectory`|String|| [Service]WorkingDirectory
|`Service_TimeoutStartSec`|String|"90s"| [Service]TimeoutStartSec



### [Install]

|name                |type    |default|description
|--------------------|--------|-------|-------------
|`Install_WantedBy`|String|[Install]WantedBy "multi-user.target"|[Install]WantedBy
|`Install_RequiredBy`|String||[Install]RequiredBy
|`Install_Also`|String||[Install]Also
|`Install_Alias`|String||[Install]Alias


> * Required

Example Playbook
----------------

    - hosts: servers
      roles:
        - role: systemd-service
          systemd_service_units:
            - name: "swarm-manager"
              envs:
                DOCKER_OPTS: "--dns 8.8.8.8"
              envs_raw:
                - "DOCKER_HOST=tcp://127.0.0.1:2375"
              Unit_Description: Docker Swarm Manager
              Unit_Requires: docker.service
              Unit_After: docker.service
              Service_ExecStartPre:
                - -/usr/bin/docker stop swarm-manager
                - -/usr/bin/docker rm swarm-manager
                - /usr/bin/docker pull swarm
              Service_ExecStart: /usr/bin/docker run -p 2377:2375 --name swarm-manager swarm manage

License
-------

MIT

Author Information
------------------

* Originally developed by @tumf
* Heavily tweaked by Freedom of the Press Foundation
