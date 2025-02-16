# Ansible

## Overview
Ansible is an open-source automation tool that provides configuration management, application deployment, task automation, and IT orchestration. It uses an agentless architecture and YAML-based playbooks to define automation tasks, making it simple to deploy and manage infrastructure and applications.

## Technical Details

### Core Components

1. **Control Node**
   - Ansible installation
   - Inventory management
   - Playbook execution
   - SSH management
   - Python runtime

2. **Managed Nodes**
   - SSH access
   - Python runtime
   - No agent required
   - Privilege escalation
   - Network connectivity

3. **Configuration Elements**
   - Inventory files
   - Playbooks
   - Roles
   - Tasks
   - Variables
   - Templates
   - Handlers

### Key Features

1. **Automation Capabilities**
   - Configuration management
   - Application deployment
   - Infrastructure provisioning
   - Security automation
   - Network automation
   - Cloud orchestration

2. **Module System**
   - Built-in modules
   - Custom modules
   - Galaxy roles
   - Collections
   - Plugin system
   - Dynamic inventory

## Implementation

### Basic Configuration

1. **Inventory Setup**
   ```yaml
   # inventory.yml
   all:
     children:
       webservers:
         hosts:
           web1.example.com:
             http_port: 80
             proxy_port: 8080
           web2.example.com:
             http_port: 80
             proxy_port: 8080
       databases:
         hosts:
           db1.example.com:
             db_port: 5432
   ```

2. **Simple Playbook**
   ```yaml
   # webserver.yml
   - name: Configure webservers
     hosts: webservers
     become: true
     
     tasks:
       - name: Install nginx
         apt:
           name: nginx
           state: present
           update_cache: yes
         
       - name: Start nginx service
         service:
           name: nginx
           state: started
           enabled: yes
         
       - name: Copy website content
         copy:
           src: files/index.html
           dest: /var/www/html/index.html
           mode: '0644'
   ```

### Advanced Configuration

1. **Role Structure**
   ```yaml
   # roles/webserver/tasks/main.yml
   - name: Install required packages
     apt:
       name: "{{ item }}"
       state: present
     loop:
       - nginx
       - php-fpm
       - php-mysql
   
   - name: Configure nginx virtual host
     template:
       src: nginx.conf.j2
       dest: /etc/nginx/sites-available/default
     notify: restart nginx
   
   # roles/webserver/handlers/main.yml
   - name: restart nginx
     service:
       name: nginx
       state: restarted
   ```

2. **Complex Playbook**
   ```yaml
   # site.yml
   - name: Configure entire application stack
     hosts: all
     become: true
     
     vars_files:
       - vars/main.yml
     
     pre_tasks:
       - name: Update apt cache
         apt:
           update_cache: yes
         when: ansible_os_family == "Debian"
     
     roles:
       - { role: common, tags: ['common'] }
       - { role: webserver, tags: ['web'] }
       - { role: database, tags: ['db'] }
     
     post_tasks:
       - name: Verify services
         command: systemctl status {{ item }}
         register: service_status
         loop:
           - nginx
           - mysql
         changed_when: false
   ```

## Design Considerations

### Architecture Planning

1. **Inventory Structure**
   - Environment separation
   - Group hierarchy
   - Host variables
   - Group variables
   - Dynamic inventory

2. **Role Design**
   - Modularity
   - Reusability
   - Dependencies
   - Defaults
   - Documentation

3. **Variable Management**
   - Variable precedence
   - Vault integration
   - Host/group vars
   - Extra vars
   - Facts usage

### Security Implementation

1. **Vault Configuration**
   ```yaml
   # Create encrypted file
   ansible-vault create secrets.yml
   
   # Edit encrypted file
   ansible-vault edit secrets.yml
   
   # Use in playbook
   - name: Deploy application
     hosts: webservers
     vars_files:
       - secrets.yml
     
     tasks:
       - name: Configure application
         template:
           src: app.conf.j2
           dest: /etc/app/config.conf
           vars:
             db_password: "{{ vault_db_password }}"
   ```

2. **SSH Management**
   ```yaml
   # ansible.cfg
   [defaults]
   host_key_checking = True
   private_key_file = ~/.ssh/ansible
   
   [ssh_connection]
   pipelining = True
   control_path = /tmp/ansible-ssh-%%h-%%p-%%r
   ```

## Best Practices

### Implementation Guidelines

1. **Project Structure**
   ```
   .
   ├── ansible.cfg
   ├── inventory/
   │   ├── production/
   │   └── staging/
   ├── group_vars/
   │   ├── all.yml
   │   └── webservers.yml
   ├── host_vars/
   │   └── web1.example.com.yml
   ├── roles/
   │   ├── common/
   │   ├── webserver/
   │   └── database/
   └── site.yml
   ```

2. **Task Organization**
   ```yaml
   # roles/webserver/tasks/main.yml
   - name: Include OS-specific variables
     include_vars: "{{ ansible_os_family }}.yml"
   
   - name: Include installation tasks
     include_tasks: install.yml
   
   - name: Include configuration tasks
     include_tasks: configure.yml
   
   - name: Include service tasks
     include_tasks: service.yml
   ```

### Operational Procedures

1. **Deployment Strategy**
   ```bash
   # Check syntax
   ansible-playbook site.yml --syntax-check
   
   # Dry run
   ansible-playbook site.yml --check
   
   # Limited run
   ansible-playbook site.yml --limit webservers
   
   # Run with tags
   ansible-playbook site.yml --tags "configuration,services"
   ```

2. **Monitoring and Debugging**
   ```bash
   # Increase verbosity
   ansible-playbook site.yml -vvv
   
   # List hosts
   ansible-inventory --list
   
   # Test connection
   ansible all -m ping
   
   # Gather facts
   ansible all -m setup
   ```

## Interview Tips
- Understand Ansible architecture
- Know playbook structure
- Explain role organization
- Understand variable precedence
- Be familiar with:
  - Module usage
  - Handler patterns
  - Inventory management
  - Vault operations
- Real-world scenarios:
  - Configuration management
  - Application deployment
  - Infrastructure provisioning
  - Security automation
- Best practices:
  - Project organization
  - Role design
  - Security implementation
  - Task modularization
- Advanced concepts:
  - Custom modules
  - Dynamic inventory
  - Callback plugins
  - Strategy plugins 