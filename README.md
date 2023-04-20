[![Build Status](https://travis-ci.org/express42/ansible-repertory.svg?branch=master)](https://travis-ci.org/express42/ansible-repertory)

# Обзор
Этот репозиторий используется в качестве хорошей отправной точки для развития инфраструктуры. Он содержит основные роли и описывает рабочий процесс для создания надежного решения.

Мастерство ансамбля определяется количеством и качеством исполняемых спектаклей. Репертори создан, чтобы включать в себя все необходимое для серверной инфраструктуры любого уровня и сложности.


## Основные принципы
* Playbook для каждого приложения
* Все переменные должны быть определены в файлах vars сред.

# Структура каталогов

* `environments/` - корневой каталог для сред
  * `environment_dir/` - каталог, содержащий переменные и хосты для каждой среды
    * `group_vars/` - переменные, определенные для каждой группы
    * `host_vars/` - переменные, определенные для хоста
    * `play_vars/` - переменные, определенные для каждой игры в playbooks
    * `inventory_file` - файл инвентаризации с хостами, относящимися к среде
* `molecule/` - конфигурация molecule v2
  * `resources/` - общие ресурсы сценариев molecule
  * `scenario_name/` - определенные сценарии molecule, разделенные по платформам, по умолчанию - контейнеры докеров
* `playbooks/` - каталог для playbooks, playbook для каждого приложения
* `roles/` - пользовательские роли
* `ansible.cfg` - конфигурация для ansible
* `requirements.txt` - python requirements
* `requirements.yml` - ansible playbooks' requirements
* `site.yml` - playbook, содержащий каждое приложение в инфраструктуре

# What goes where

## Variables
* All variables should be defined per environment
* The precedence order can be seen at http://docs.ansible.com/ansible/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable
* Try to use only next places to define a variable:
  * Environment host_vars
  * Environment group_vars
  * Environment play_vars
* Avoid setting variable in playbooks

## Tasks
* All tasks should be defined in roles

## Playbooks
* Playbooks contain only roles, groups of hosts and vars files
* Playbook should contain everything needed for application to start
* Avoid setting variables and tasks in playbooks
* You can use `site.yml` as the only entrypoint for your configuration

# Getting started

## Fast start
* Do not forget use Python 2.7 instead of 3.x. Molecule and Ansible doesn't support Python 3.x
* Clone git repo
```sh
git clone git@github.com:express42/ansible-repertory.git
``` 
* Install [the latest release of Vagrant][Vagrant] if you will use ansible-repertory with Parallels VMs
* Then install Vagrant plugin for Parallels
```sh
vagrant plugin install vagrant-parallels
```
* Install prerequisites
```sh
cd ansible-repertory
ansible-galaxy install -r requirements.yml
pip install -r requirements.txt
touch vault.key
```
* Run tests
```sh
molecule test
```

## Using with your infrastructure
* Remove excessive roles from requirements.yml, python modules from requirements.txt and playbooks
* Make changes to site.yml
* Make changes to vars files in `example` environment
* Test changes with command
```sh
molecule test
```
* Copy molecule environment to new environment
* Make changes to inventory and vars files
* Run command
```sh
ansible-playbook site.yml --inventory-file=./environments/new_environment/inventory
```
## Molecule

```sh
molecule list
molecule converge
molecule status
molecule login common
molecule destroy
```

# Testing using Travis-CI
* Fork git repository
* Add repository into Travis-CI
* Push new commits

# ToDo
* Update default packages playbook
* Add more playbooks or list recommended ones
* Add base role (as it is in testo)

# Known issues
* 'zabbix_url' variable used in playbooks dj-wasabi.zabbix-agent and dj-wasabi.zabbix-server for different purposes
  * Workaround: redefine 'zabbix_url' in play_vars
* API calls in playbook dj-wasabi.zabbix-agent made from several servers can interfere with each other
  * Workaround: make API calls serial
* While using VirtualBox for testing you should change all mentions of interface 'eth0' to 'eth1'

# Links
* [Ansible documentation](https://docs.ansible.com/ansible/index.html "Ansible documentation")
* [Molecule documentation](https://molecule.readthedocs.io/en/master/ "Molecule documentation")
* [Vagrant documentation](https://www.vagrantup.com/docs/ "Vagrant documentation")

[Vagrant]: https://www.vagrantup.com/downloads.html "Vagrant"
