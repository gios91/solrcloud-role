# SolrCloud Ansible role (AWS EC2 version with SolrCloud 5.5.1)

This ansible role installs a SolrCloud server in a debian environment.

- [Getting Started](#getting-started)
	- [Prerequisities](#prerequisities)
	- [Installing](#installing)
- [Usage](#usage)
- [Testing](#testing)
- [Built With](#built-with)
- [Versioning](#versioning)
- [Authors](#authors)
- [License](#license)
- [Contributing](#contributing)

## Getting Started

These instructions will get you a copy of the role for your ansible playbook. Once launched, it will install a [SolrCloud](https://cwiki.apache.org/confluence/display/solr/SolrCloud) server in a Debian system.

### Prerequisities

Ansible 2.5.5.0 version installed.
Inventory destination should be a Debian environment.

For testing purposes, [Molecule](https://molecule.readthedocs.io/) with [Docker](https://www.docker.com/) as driver.

### Installing

Create or add to your roles dependency file (e.g requirements.yml):

```
- src: idealista.solrcloud-role
  version: 2.0.0
  name: solrcloud
```

Install the role with ansible-galaxy command:

```
ansible-galaxy install -p roles -r requirements.yml -f
```

Use in a playbook:

```
---
- hosts: someserver
  roles:
    - { role: solrcloud }
```

Playbook example below showing how to provision from scratch a SolrCloud cluster with two nodes plus create an example (and empty) collection called `sample_techproducts_configs`, using idealista [java](https://github.com/idealista/java-role), [zookeeper](https://github.com/idealista/zookeeper-role) and [solrcloud](https://github.com/idealista/solrcloud-role) roles:

**Note:** Assuming that 'solrcloud' group has two nodes (`solrcloud1` and `solrcloud2`) as is declared in [molecule.yml](https://github.com/idealista/solrcloud-role/tree/master/molecule/default/molecule.yml),
collection will have two shards, one replica and one shard per node as is declared in group vars file called [solrcloud.yml](https://github.com/idealista/solrcloud-role/tree/master/molecule/default/group_vars/solrcloud.yml)
and configuration files are stored under directory called `sample_techproducts_configs` under template directory.

> :warning: Use the example below just as a reference, requires inventory host groups `solr` and `zookeeper` to be correctly defined

```
---

- hosts: zookeeper
  roles:
    - role: zookeeper
  pre_tasks:
    - name: installing required libs
      apt:
        pkg: "{{ item }}"
        state: present
      with_items:
        - net-tools
        - netcat

- hosts: solrcloud
  roles:
    - role: solrcloud-role
```

## Usage

Look to the defaults properties file to see the possible configuration properties.

## Testing

```
$ pipenv install -r test-requirements.txt -python 2.7

# This will execute tests but doesn't destroy created environment (because of --destroy=never)
$ pipenv run molecule test --destroy=never
```

Solr Admin UI should be accessible from docker container host at URL:

http://localhost:8983/solr/#/ (node: `solrcloud1`)

or

http://localhost:8984/solr/#/ (node: `solrcloud2`)

<img src="https://raw.githubusercontent.com/idealista/solrcloud-role/master/assets/solr_admin_ui.png" alt="Solr Admin UI example" style="width: 600px;"/>

See [molecule.yml](https://github.com/idealista/solrcloud-role/blob/master/molecule/default/molecule.yml) to check possible testing platforms.

## Built With

![Ansible](https://img.shields.io/badge/ansible-2.5.5.0-green.svg)

## Versioning

For the versions available, see the [tags on this repository](https://github.com/idealista/solrcloud-role/tags).

Additionaly you can see what change in each version in the [CHANGELOG.md](https://github.com/idealista/solrcloud-role/blob/master/CHANGELOG.md) file.

## Authors

* **Idealista** - *Work with* - [idealista](https://github.com/idealista)

See also the list of [contributors](https://github.com/idealista/solrcloud-role/contributors) who participated in this project.

## License

![Apache 2.0 Licence](https://img.shields.io/hexpm/l/plug.svg)

This project is licensed under the [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0) license - see the [LICENSE](LICENSE) file for details.
```

# Steps for SolrCloud setup on YNAP EMR cluster

- 1. Connect via SSH on all cluster nodes
```
mv aws-key.pem ~/.ssh/
chmod 600 ~/.ssh/aws-key.pem
```

```
for all nodes: 
ssh -i ~/.ssh/aws-key.pem hadoop@NODE_IP
```

- 2. Install Ansible on Master node (Centos 6)

```
yum install epel-release
yum-config-manager --enable epel
yum update && yum install ansible 
```

- 3. Setup Hosts  

create inventory.yml file with hosts and link to ssh_key

```
[hosts:vars]
ansible_ssh_private_key_file = PATH_TO_PEM_KEY

[hosts]
ip1
ip2
ipN
```

- 4. Install solr-cloud role

create a file requirements.yml with following content:

- src: idealista.solrcloud-role
  version: 1.8.0
  name: solrcloud

```
sudo ansible-galaxy install -p roles -r requirements.yml -f --ignore-certs
```

execute playbook

# Solr bin path on AWS EC2

```
./opt/solr-5.5.1/bin/solr
```

for info follow: 
	install and setup ansible: https://community.spiceworks.com/how_to/110616-install-ansible-on-64-bit-centos-6-6
	configure use epel repo: https://linuxacademy.com/community/posts/show/topic/25457-i-could-not-install-ansible-in-aws-instance-ec2

