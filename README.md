# Ansible Roles

Shareable Ansible roles used for multiple use cases inside Basho.

1. [Prerequisites](#prerequisites)
1. [Role Dependencies](#role-dependencies)
1. [Installation](#installation)
1. [Client Library Environments](#client-library-environments)
1. [Playbook Examples](#playbook-examples)
1. [Contributing](#contributing)
1. [License and Authors](#license-and-authors)

## Prerequisites

- Ansible v2.1 or higher

## Role Dependencies

This collection of roles has various dependencies that can all be retrieved from Ansible Galaxy using the following command line:

```bash
ansible-galaxy install basho-labs.riak-kv geerlingguy.php geerlingguy.composer joshualund.golang geerlingguy.java geerlingguy.repo-remi geerlingguy.nodejs geerlingguy.ruby
```

## Installation

There are two ways you can use the roles from this repository.

- Option A is to use these roles as the primary set of roles for your playbooks by cloning this repo as `roles` within the same directory as your playbooks.
- Option B is to include these in addition to your primary roles and define the path to these roles within your `ansible.cfg` file using the option [`roles_path`](http://docs.ansible.com/ansible/intro_configuration.html#roles-path).

## Client Library Environments

* PHP
* Go
* Ruby
* Java
* NodeJS
* Python
* .NET
* Erlang

## Playbook Examples

The following are several examples of how to use these Ansible roles in your environment

### Riak KV Testing

**Install / configure Riak via PackageCloud & Execute client library tests**

```yml
---
- hosts: localhost
  become: true
  become_method: sudo
  roles:
    - {
        role: riak_testing,
        riak_conf_template: '/vagrant/ansible-roles/riak_testing/templates/riak.conf.j2',
        riak_node_name: "riak@127.0.0.1",
      }
    - {
        role: client_smoke_tests,
        ct_test_libs: ['go','java','php','ruby','nodejs']
      }
  vars:
    riak_search:  'on'
    riak_backend: 'multi'
    riak_pb_bind_ip: 127.0.0.1

    # setup Riak environment (bucket-types, users, sources, grants, conf)
    riak_bucket_types:
      - { name: bitcask, props: '{"props":{"backend":"bitcask_backend"}}' }
      - { name: counters, props: '{"props":{"datatype":"counter"}}' }
      - { name: hlls, props: '{"props":{"datatype":"hll"}}' }
      - { name: leveldb_type, props: '' }
      - { name: maps, props: '{"props":{"datatype":"map"}}' }
      - { name: memory_type, props: '{"props":{"backend":"mem_backend"}}' }
      - { name: mr, props: '' }
      - { name: no_siblings, props: '{"props":{"allow_mult":"false"}}' }
      - { name: other_counters, props: '{"props":{"datatype":"counter"}}' }
      - { name: plain, props: '' }
      - { name: search_type, props: '' }
      - { name: sets, props: '{"props":{"datatype":"set"}}' }
      - { name: write_once, props: '{"props":{"write_once":true}}' }
      - { name: yokozuna, props: '' }

      # PHP Testing Buckets
      - { name: phptest_counters,  props: '{"props":{"datatype":"counter"}}' }
      - { name: phptest_leveldb, props: '' }
      - { name: phptest_maps, props: '{"props":{"datatype":"map"}}' }
      - { name: phptest_search, props: '' }
      - { name: phptest_sets, props: '{"props":{"datatype":"set"}}' }
```

**Execute client library tests against existing Riak instance / cluster**

```yml
---
- hosts: localhost
  become: true
  become_method: sudo
  roles:
    - {
        role: client_smoke_tests,
        ct_test_libs: ['go','java','php','ruby','nodejs'],
        ct_riak_host: 'myriakcluster.local.com',
        ct_riak_port: 8087,
        ct_riak_http_port: 8098
      }
```

### Riak TS Testing

```yml
---
- hosts: localhost
  become: true
  become_method: sudo
  roles:
    - {
        role: riak_testing,
        riak_conf_template: '/vagrant/ansible-roles/riak_testing/templates/riak.conf.j2',
        riak_node_name: "riak@127.0.0.1",
      }
    - {
        role: client_smoke_tests,
        ct_test_libs: ['go','java','php','ruby','nodejs']
      }
  vars:
    riak_package: 'riak-ts'
    riak_shell_group: 'riak-ts'
    ct_cmd: cmdts

    riak_search:  'on'
    riak_backend: 'multi'
    riak_pb_bind_ip: 127.0.0.1

    # setup Riak environment (bucket-types, users, sources, grants, conf)
    riak_bucket_types:
      #- { name: bitcask, props: '{"props":{"backend":"bitcask_backend"}}' }
      - { name: counters, props: '{"props":{"datatype":"counter"}}' }
      - { name: leveldb_type, props: '' }
      - { name: maps, props: '{"props":{"datatype":"map"}}' }
      - { name: mr, props: '' }
      - { name: no_siblings, props: '{"props":{"allow_mult":"false"}}' }
      - { name: other_counters, props: '{"props":{"datatype":"counter"}}' }
      - { name: plain, props: '' }
      - { name: search_type, props: '' }
      - { name: sets, props: '{"props":{"datatype":"set"}}' }
      - { name: write_once, props: '{"props":{"write_once":true}}' }
      - { name: yokozuna, props: '' }

      # TS
      - { name: GeoCheckin, props: '{"props": {"n_val": 3, "table_def": "CREATE TABLE GeoCheckin (geohash varchar not null, user varchar not null, time timestamp not null, weather varchar not null, temperature double, PRIMARY KEY((geohash, user, quantum(time, 15, m)), geohash, user, time))"}}' }
      - { name: GeoCheckin_Wide, props: '{"props": {"n_val": 3, "table_def": "CREATE TABLE GeoCheckin_Wide (geohash varchar not null, user varchar not null, time timestamp not null, weather varchar not null, temperature double, uv_index sint64, observed boolean not null, PRIMARY KEY((geohash, user, quantum(time, 15, m)), geohash, user, time))"}}' }
      - { name: WeatherByRegion, props: '{"props": {"n_val": 3, "table_def": "CREATE TABLE WeatherByRegion (region varchar not null, state varchar not null, time timestamp not null, weather varchar not null, temperature double, uv_index sint64, observed boolean not null, PRIMARY KEY((region, state, quantum(time, 15, m)), region, state, time))"}}' }
```

### Riak Security Testing

```yml
---
- hosts: localhost
  become: true
  become_method: sudo
  roles:
    - {
        role: riak_testing,
        riak_conf_template: '/vagrant/ansible-roles/riak_testing/templates/riak.conf.j2',
        riak_node_name: "riak@127.0.0.1",
      }
    - {
        role: client_smoke_tests,
        ct_test_libs: ['go','java','php','ruby','nodejs']
      }
  vars:
    riak_package: 'riak-ts'
    riak_shell_group: 'riak-ts'
    ct_cmd: cmdts

    riak_search:  'on'
    riak_backend: 'multi'
    riak_pb_bind_ip: 127.0.0.1

    riak_security_enabled:  true
    riak_testing_certs_dir: '/vagrant/tools/test-ca'

    # setup Riak environment (bucket-types, users, sources, grants, conf)
    riak_bucket_types:
      - { name: bitcask, props: '{"props":{"backend":"bitcask_backend"}}' }
      - { name: counters, props: '{"props":{"datatype":"counter"}}' }
      - { name: hlls, props: '{"props":{"datatype":"hll"}}' }
      - { name: leveldb_type, props: '' }
      - { name: maps, props: '{"props":{"datatype":"map"}}' }
      - { name: memory_type, props: '{"props":{"backend":"mem_backend"}}' }
      - { name: mr, props: '' }
      - { name: no_siblings, props: '{"props":{"allow_mult":"false"}}' }
      - { name: other_counters, props: '{"props":{"datatype":"counter"}}' }
      - { name: plain, props: '' }
      - { name: search_type, props: '' }
      - { name: sets, props: '{"props":{"datatype":"set"}}' }
      - { name: write_once, props: '{"props":{"write_once":true}}' }
      - { name: yokozuna, props: '' }

      # PHP Testing Buckets
      - { name: phptest_counters,  props: '{"props":{"datatype":"counter"}}' }
      - { name: phptest_leveldb, props: '' }
      - { name: phptest_maps, props: '{"props":{"datatype":"map"}}' }
      - { name: phptest_search, props: '' }
      - { name: phptest_sets, props: '{"props":{"datatype":"set"}}' }

    riak_users:
      - {user: 'riakuser', password: '', cert: '', groups: ''}
      - {user: 'riakpass', password: 'Test1234', cert: '', groups: ''}
      - {user: 'riakadmin', password: '', cert: '', groups: 'admins'}
      - {user: 'riakdeveloper', password: '', cert: '', groups: 'developers'}

    riak_groups:
      - admins
      - developers

    riak_sources:
      - {user: 'riakuser', type: 'certificate', cidr: '0.0.0.0/0'}
      - {user: 'riakpass', type: 'password', cidr: '0.0.0.0/0'}

    riak_grants:
      - {subject: 'riakuser', scope: 'any', permissions: '{{ it_permissions|join(",")}}'}
      - {subject: 'riakpass', scope: 'any', permissions: '{{ it_permissions|join(",")}}'}
```

## Contributing

Basho Labs repos survive because of community contribution. Review the details in [CONTRIBUTING.md](CONTRIBUTING.md) in order to give back to this project.

## License and Authors

The riak-zend-zray project is Open Source software released under the Apache 2.0 License. Please see the [LICENSE](LICENSE) file for full license details.

* Author: [Christopher Mancini](https://github.com/christophermancini)
