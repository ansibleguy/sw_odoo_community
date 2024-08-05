<a href="https://www.odoo.com/">
<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/50/Odoo_logo.svg/2560px-Odoo_logo.svg.png" alt="Odoo logo" width="300"/>
</a>

# Ansible Role - Provision Odoo Community

Role to deploy [Odoo Community-Edition](https://www.odoo.com/documentation/17.0/administration/on_premise.html)

<a href='https://ko-fi.com/ansible0guy' target='_blank'><img height='35' style='border:0px;height:46px;' src='https://az743702.vo.msecnd.net/cdn/kofi3.png?v=0' border='0' alt='Buy me a coffee' />

[![Molecule Test Status](https://badges.ansibleguy.net/sw_odoo.molecule.svg)](https://github.com/ansibleguy/_meta_cicd/blob/latest/templates/usr/local/bin/cicd/molecule.sh.j2)
[![YamlLint Test Status](https://badges.ansibleguy.net/sw_odoo.yamllint.svg)](https://github.com/ansibleguy/_meta_cicd/blob/latest/templates/usr/local/bin/cicd/yamllint.sh.j2)
[![PyLint Test Status](https://badges.ansibleguy.net/sw_odoo.pylint.svg)](https://github.com/ansibleguy/_meta_cicd/blob/latest/templates/usr/local/bin/cicd/pylint.sh.j2)
[![Ansible-Lint Test Status](https://badges.ansibleguy.net/sw_odoo.ansiblelint.svg)](https://github.com/ansibleguy/_meta_cicd/blob/latest/templates/usr/local/bin/cicd/ansiblelint.sh.j2)
[![Ansible Galaxy](https://badges.ansibleguy.net/galaxy.badge.svg)](https://galaxy.ansible.com/ui/standalone/roles/ansibleguy/sw_odoo)

Molecule Logs: [Short](https://badges.ansibleguy.net/log/molecule_sw_odoo_test_short.log), [Full](https://badges.ansibleguy.net/log/molecule_sw_odoo_test.log)

**Tested:**
* Debian 12

## Install

```bash
# latest
ansible-galaxy role install git+https://github.com/ansibleguy/sw_odoo

# from galaxy
ansible-galaxy install ansibleguy.sw_odoo

# or to custom role-path
ansible-galaxy install ansibleguy.sw_odoo --roles-path ./roles

# install dependencies
ansible-galaxy install -r requirements.yml
```

----

## Usage

### Config

```yaml
odoo:
  hostnames: 'erp.template.ansibleguy.net'
  admin_pwd: !vault |
    ...

  db:
    pwd: !vault |
      ...
```

You might want to use 'ansible-vault' to encrypt your passwords:
```bash
ansible-vault encrypt_string
```

### Execution

Run the playbook:
```bash
ansible-playbook -K -D -i inventory/hosts.yml playbook.yml
```

There are also some useful **tags** available:
* webserver

To debug errors - you can set the 'debug' variable at runtime:
```bash
ansible-playbook -K -D -i inventory/hosts.yml playbook.yml -e debug=yes
```


----

## Functionality

* **Package installation**
  * Ansible dependencies (_minimal_)


* **Configuration**
  * **Default config**:
    * Auto-generated passwords
 

  * **Default opt-ins**:
    * Install postgresql database
    * Install nginx webserver

----

## Info

* **Note:** this role currently only supports debian-based systems


* **Note:** Most of the role's functionality can be opted in or out.

  For all available options - see the default-config located in [the main defaults-file](https://github.com/ansibleguy/sw_odoo/blob/latest/defaults/main/1_main.yml)!


* **Warning:** Not every setting/variable you provide will be checked for validity. Bad config might break the role!


* **Warning:** Make sure to only allow Port 80/443 to the server as odoo will listen on 8069 and optionally 8072!


* **Note:** You might want to [block login-failure using Fail2Ban](https://www.odoo.com/documentation/17.0/administration/on_premise/deploy.html#blocking-brute-force-attacks)
