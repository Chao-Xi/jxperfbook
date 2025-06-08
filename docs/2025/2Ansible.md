# 1 Ansible Interview Question

### 1. What is Ansible and how does it work?

Ansible is an open-source configuration management, application deployment, and task automation tool. 


It works by connecting to nodes over SSH (or WinRM for Windows), **pushing small programs called 'modules' to these nodes, and executing them**. 

**The modules are removed once the task is complete**. Ansible uses YAML-based playbooks for automation.

### 2. How can you make Ansible idempotent?

Ansible modules are designed to be idempotent by default, meaning running the same playbook multiple times won't change the system unless there is a change in configuration.

To ensure idempotence, avoid using shell or command modules unless absolutely necessary and rely on modules like `yum`, `copy`, `file`, `service`, etc

#### 3. Scenario: You need to deploy a web application to 100 servers, but 20 are not reachable. How do you handle this in Ansible?

Use `serial` in the playbook to limit the number of servers deployed at a time and handle unreachable hosts gracefully:


```
- hosts: web_servers
  serial: 10
  ignore_unreachable: yes
  tasks:
  - name: Deploy web app
    include_role:
    name: web_app
```

You can also use `--limit` and `--start-at-task` to resume deployments.


#### 4. What are Ansible roles and how do they help in playbook structuring?

- Roles are a way to organize playbooks into reusable components. 
- Each role has a specific structure with directories **like tasks, handlers, templates, files, vars, and defaults.**
- Roles promote reusability, modularity, and clean separation of concerns in complex environments.

#### 5. How does Ansible differ from other configuration management tools like Puppet or Chef?

- Ansible is agentless and uses YAML for playbook definitions, making it easier to learn and use. 
- **Puppet and Chef require agents and use Ruby DSL.**
- **Ansible's push-based model over SSH contrasts with Puppet’s pull model**

#### 6. Scenario: How would you implement dynamic inventory in Ansible for AWS EC2 instances

Use the `ec2.py` dynamic inventory script or the `amazon.aws.aws_ec2` inventory plugin. 

Set up the required AWS credentials and use filters to dynamically target specific instances.

**Example in `ansible.cfg`**

···
[inventory]
enable_plugins = aws_ec2
···

Then configure the plugin in `aws_ec2.yaml`.


#### 7. How do you handle secret variables in Ansible?

**Use `ansible-vault` to encrypt secrets like passwords, API keys, etc.** 

**Create an encrypted file using: `ansible-vault create secrets.yml`**


**Decrypt with `--ask-vault-pass` or use a vault password file. Secrets can also be encrypted inline using `!vault`**

#### 8. What are Ansible facts and how are they used in playbooks?

Facts are system properties collected by the `setup` module. 

**They include details like OS, IP, memory, etc., and are accessible via `hostvars` or directly as variables (e.g.,`ansible_os_family`).**

You can disable them using `gather_facts: no`.

#### 9. Scenario: You need to execute a task only if a specific file exists on the target host. How do you do it?

Use a conditional with the `stat` module to check file existence:

···
- name: Check if config file exists
  stat:
    path: /etc/myapp/config.ini
    register: config_file
    
- name: Run task if file exists
  command: /usr/local/bin/apply_config
  when: config_file.stat.exists
···


#### 10. What are callback plugins in Ansible?

Callback plugins control how Ansible displays output. 

Examples include `default`, `json`,`yaml`, and `minimal`. You can also write custom plugins to log or process events in custom formats.

#### 11. How can you optimize playbook performance?

Use strategies like:

- **Reducing unnecessary fact gathering**
- **Using `block` and `when` to avoid unneeded tasks**
- Limiting hosts with `--limit`
- **Running tasks in parallel with `forks`**
- Using `async` for long tasks
- Leveraging `free` strategy for non-blocking execution

### 12. Scenario: You want to restart a service only when its configuration file changes. How do you do that?

Use handlers triggered by tasks. Example:

```
- name: Copy config file
  template:
    src: app.conf.j2
    dest: /etc/app/app.conf
  notify: Restart app

handlers:
- name: Restart app
  service:
    name: app
    state: restarted
```

#### 13. What is the difference between `include` and `import` in Ansible?

**`import_tasks` and `import_playbook` are static, evaluated at playbook parse time.**


**`include_tasks` and `include_playbook` are dynamic, evaluated at runti**me. Use `include`
when you need conditionals or loops around task files.

#### 14. How do you use Jinja2 templates in Ansible?


Templates are used for dynamically generating configuration files. They use `.j2` extension
and allow variables, loops, and conditionals. Example:

`{{ ansible_hostname }} - {{ inventory_hostname }}`

#### 15. Scenario: You need to run different tasks on RHEL and Ubuntu systems. How would you handle it?

Use conditionals based on `ansible_os_family`:

···
- name: Install package
yum:
name: httpd
state: present
when: ansible_os_family == 'RedHat'

- name: Install package
apt:
name: apache2
state: present
when: ansible_os_family == 'Debian'
···

#### 16. How does Ansible Tower differ from Ansible Core?

Ansible Tower (now part of Red Hat Automation Platform) is a web UI and REST API
interface for Ansible Core. 

It adds features like RBAC, job scheduling, logging, credentials
management, and workflow support

#### 18. What is a vault ID and why is it useful?

Vault ID allows multiple vault passwords to be used in a single playbook run, useful when
encrypting different variables with different keys. 


Command: `--vault-id dev@prompt --vault-id prod@prompt`

#### 19. What is the difference between `delegate_to` and `local_action`?

`delegate_to` runs a task on another host (can be remote or localhost), while `local_action` is
shorthand to run on localhost. 


Use `delegate_to` when you want remote delegation.