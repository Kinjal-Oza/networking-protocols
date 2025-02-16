# Puppet

## Overview
Puppet is a configuration management tool that enables system administrators to automate the provisioning, configuration, and management of servers and applications. It uses a declarative language to define system configuration and ensures systems maintain their desired state through continuous enforcement.

## Technical Details

### Core Components

1. **Puppet Server**
   - Certificate Authority
   - Catalog compilation
   - Node classification
   - Report processing
   - API endpoints
   - PuppetDB integration

2. **Puppet Agent**
   - Configuration pull
   - Fact collection
   - Catalog application
   - Report submission
   - Resource management
   - Service management

3. **Configuration Elements**
   - Manifests
   - Modules
   - Classes
   - Resources
   - Facts
   - Templates
   - Hiera data

### Key Features

1. **Management Capabilities**
   - Configuration enforcement
   - Package management
   - Service management
   - File management
   - User management
   - Security policies

2. **Module System**
   - Forge modules
   - Custom modules
   - Module dependencies
   - Data separation
   - Template rendering
   - Resource types

## Implementation

### Basic Configuration

1. **Simple Manifest**
   ```puppet
   # site.pp
   node 'webserver.example.com' {
     class { 'apache':
       default_vhost => false,
       mpm_module    => 'prefork',
     }
     
     apache::vhost { 'example.com':
       port    => '80',
       docroot => '/var/www/html',
     }
     
     package { 'php':
       ensure => installed,
     }
     
     service { 'php-fpm':
       ensure => running,
       enable => true,
     }
   }
   ```

2. **Class Definition**
   ```puppet
   # modules/webserver/manifests/init.pp
   class webserver (
     String $docroot = '/var/www/html',
     Integer $port = 80,
     String $server_name = $facts['fqdn'],
   ) {
     package { ['nginx', 'php-fpm']:
       ensure => installed,
     }
     
     file { $docroot:
       ensure => directory,
       owner  => 'www-data',
       group  => 'www-data',
       mode   => '0755',
     }
     
     file { '/etc/nginx/sites-available/default':
       ensure  => file,
       content => template('webserver/vhost.erb'),
       notify  => Service['nginx'],
     }
     
     service { ['nginx', 'php-fpm']:
       ensure => running,
       enable => true,
     }
   }
   ```

### Advanced Configuration

1. **Defined Type**
   ```puppet
   # modules/apache/manifests/vhost.pp
   define apache::vhost (
     String $docroot,
     Integer $port = 80,
     String $server_name = $name,
     Boolean $ssl = false,
   ) {
     include apache
     
     file { "/etc/apache2/sites-available/${name}.conf":
       ensure  => file,
       content => template('apache/vhost.erb'),
       notify  => Service['apache2'],
     }
     
     file { "/etc/apache2/sites-enabled/${name}.conf":
       ensure => link,
       target => "/etc/apache2/sites-available/${name}.conf",
       notify => Service['apache2'],
     }
     
     if $ssl {
       include apache::ssl
     }
   }
   ```

2. **Hiera Configuration**
   ```yaml
   # data/common.yaml
   ---
   apache::mpm_module: 'prefork'
   apache::default_vhost: false
   apache::timeout: 120
   
   # data/nodes/webserver.example.com.yaml
   ---
   classes:
     - apache
     - mysql::server
     - php
   
   apache::vhosts:
     'example.com':
       port: 80
       docroot: '/var/www/html'
       ssl: false
   ```

## Design Considerations

### Architecture Planning

1. **Environment Structure**
   - Production
   - Staging
   - Development
   - Testing
   - Module paths
   - Data separation

2. **Module Design**
   - Modularity
   - Reusability
   - Dependencies
   - Parameters
   - Documentation

3. **Data Management**
   - Hiera hierarchy
   - Data sources
   - Lookup paths
   - Secret management
   - Variable scope

### Security Implementation

1. **Certificate Management**
   ```puppet
   # puppet.conf
   [master]
   dns_alt_names = puppet,puppet.example.com
   certname = puppet.example.com
   
   [agent]
   server = puppet.example.com
   certname = %{trusted.certname}
   ```

2. **Access Control**
   ```puppet
   # auth.conf
   path /puppet/v3/environments
   method find, search
   auth any
   allow *
   
   path /puppet/v3/catalog/
   method find
   auth yes
   allow nodes
   ```

## Best Practices

### Implementation Guidelines

1. **Project Structure**
   ```
   .
   ├── data/
   │   ├── common.yaml
   │   └── nodes/
   ├── environment.conf
   ├── hiera.yaml
   ├── manifests/
   │   └── site.pp
   └── modules/
       ├── apache/
       ├── mysql/
       └── php/
   ```

2. **Resource Management**
   ```puppet
   # Good practice
   package { 'nginx':
     ensure => installed,
     before => File['/etc/nginx/nginx.conf'],
   }
   
   file { '/etc/nginx/nginx.conf':
     ensure  => file,
     content => template('nginx/nginx.conf.erb'),
     notify  => Service['nginx'],
   }
   
   service { 'nginx':
     ensure => running,
     enable => true,
   }
   ```

### Operational Procedures

1. **Testing Strategy**
   ```bash
   # Validate syntax
   puppet parser validate manifests/site.pp
   
   # Test compilation
   puppet apply --noop manifests/site.pp
   
   # Run rspec tests
   cd modules/apache
   puppet-rspec
   ```

2. **Deployment Process**
   ```bash
   # Deploy new module
   puppet module install puppetlabs-apache
   
   # Run Puppet agent
   puppet agent -t
   
   # Check reports
   puppet report show
   ```

## Interview Tips
- Understand Puppet architecture
- Know manifest structure
- Explain module organization
- Understand resource ordering
- Be familiar with:
  - Resource types
  - Class inheritance
  - Defined types
  - Fact system
- Real-world scenarios:
  - Configuration management
  - Application deployment
  - Security compliance
  - System updates
- Best practices:
  - Code organization
  - Module design
  - Testing strategy
  - Documentation
- Advanced concepts:
  - Custom facts
  - Custom types
  - Custom providers
  - Report processors 