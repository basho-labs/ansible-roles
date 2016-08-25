# Ansible Roles

Shareable Ansible roles used for multiple use cases inside Basho.

1. [Prerequisites](#prerequisites)
1. [Installation](#installation)
1. [Client Library Environments](#client-library-environments)
1. [Playbook Examples](#playbook-examples)
1. [Contributing](#contributing)
1. [License and Authors](#license-and-authors)

## Prerequisites

  - Ansible v2 or higher

## Installation

There are two ways you can use the roles from this repository. Option A is to use these roles as the primary set of roles for your playbooks by cloning this repo as `roles` within the same directory as your playbooks. Option B is to include these in addition to your primary roles and define the path to these roles within your `ansible.cfg` file using the option [`roles_path`](http://docs.ansible.com/ansible/intro_configuration.html#roles-path).

## Client Library Environments

* PHP
* Go
* .NET
* Ruby
* Python
* Java
* Node
* Erlang

## Playbook Examples

The following are several examples of how to use these Ansible roles in your environment

### Riak KV Testing

### Riak TS Testing

### Riak Security Testing

## Contributing

Basho Labs repos survive because of community contribution. Review the details in [CONTRIBUTING.md](CONTRIBUTING.md) in order to give back to this project.

## License and Authors

The riak-zend-zray project is Open Source software released under the Apache 2.0 License. Please see the [LICENSE](LICENSE) file for full license details.

* Author: [Christopher Mancini](https://github.com/christophermancini)
