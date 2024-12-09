# ansible-role-awx_sync_job_templates

Role to create and maintain AWX Job Templates based on metadata found in playbooks.
It can also assign permissions for created Job Templates. It works for playbooks, whoose first Play
contains one of the 2 predefined variables: `awx_job_template_spec`, `awx_job_template_access`.

## Table of content

- [Requirements](#requirements)
- [Default Variables](#default-variables)
  - [awx_sync_job_templates_manage_self](#awx_sync_job_templates_manage_self)
  - [awx_sync_job_templates_playbooks_path](#awx_sync_job_templates_playbooks_path)
  - [awx_sync_job_templates_project_root](#awx_sync_job_templates_project_root)
  - [playbook](#playbook)
- [Dependencies](#dependencies)
- [License](#license)
- [Author](#author)

---

## Requirements

- Minimum Ansible version: `2.10`

## Default Variables

### awx_sync_job_templates_manage_self

This boolean controls whether this role is allowed to manage Job Template linked with playbook it is called from.
By default it is not allowed, in order not to lock itself out due to some errors in playbook. This is especially important,
if it is called with elevated API permissions and there would be no manual way to correct back Job Template definition.

#### Default value

```YAML
awx_sync_job_templates_manage_self: false
```

### awx_sync_job_templates_playbooks_path

Directory where to look for playbooks.

#### Default value

```YAML
awx_sync_job_templates_playbooks_path: '{{ awx_sync_job_templates_project_root }}/playbooks'
```

### awx_sync_job_templates_project_root

Base directory of Project in Execution Environment. In AWX default EE current project is located under `/runner/project`

#### Default value

```YAML
awx_sync_job_templates_project_root: /runner/project
```

### playbook

This is not a valid variable, but rather hint how to instrument other playbooks, so that this role creates proper Job Templates
for them automatically. You just need to declare `awx_job_template_spec` and optionally `awx_job_template_access` variables
in **first** play of the playbook.

#### Example usage

```YAML
---
- name: Name of the first play, that will be the default Job Template name unless specified directly below
  hosts: localhost
  become: false
  vars:
    awx_job_template_spec:
      name: Job Template name (other than first play name)
      description: Does just show usage of this role
      # ...all other options accepted by awx.awx.job_template, like for example:
      survey_spec:
        name: ""
        description: ""
        spec:  # can be taken from /api/v2/job_templates/x/survey_spec after converting to yaml
          - question_name: 'Question'
            variable: var_name
            default: "Answer"
            type: text
            required: false
    awx_job_template_access:
      - role: execute     # valid role name for Job Template - see awx.awx.role
        state: present    # default
        teams:            # at least one of teams/users should be specified
          - team1
        users:
          - user1
  tasks:
    - ...your playbook code here
```



## Dependencies

- awx.awx

## License

GPL-3.0-or-later

## Author

[bartowl](nmailto:github@bartowl.eu)
