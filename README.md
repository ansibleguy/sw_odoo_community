<a href="https://www.odoo.com/">
<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/50/Odoo_logo.svg/2560px-Odoo_logo.svg.png" alt="Odoo logo" width="300"/>
</a>

# Ansible Role - Provision Odoo Community

Role to deploy [Odoo Community-Edition](https://www.odoo.com/documentation/17.0/administration/on_premise.html)

This role will work to install a self-hosted enterprise-edition installation - but will not completely automate it, as you need a custom setup-binary for it.

<a href='https://ko-fi.com/ansible0guy' target='_blank'><img height='35' style='border:0px;height:46px;' src='https://az743702.vo.msecnd.net/cdn/kofi3.png?v=0' border='0' alt='Buy me a coffee' />

[![Molecule Test Status](https://badges.ansibleguy.net/sw_odoo_community.molecule.svg)](https://github.com/ansibleguy/_meta_cicd/blob/latest/templates/usr/local/bin/cicd/molecule.sh.j2)
[![YamlLint Test Status](https://badges.ansibleguy.net/sw_odoo_community.yamllint.svg)](https://github.com/ansibleguy/_meta_cicd/blob/latest/templates/usr/local/bin/cicd/yamllint.sh.j2)
[![PyLint Test Status](https://badges.ansibleguy.net/sw_odoo_community.pylint.svg)](https://github.com/ansibleguy/_meta_cicd/blob/latest/templates/usr/local/bin/cicd/pylint.sh.j2)
[![Ansible-Lint Test Status](https://badges.ansibleguy.net/sw_odoo_community.ansiblelint.svg)](https://github.com/ansibleguy/_meta_cicd/blob/latest/templates/usr/local/bin/cicd/ansiblelint.sh.j2)
[![Ansible Galaxy](https://badges.ansibleguy.net/galaxy.badge.svg)](https://galaxy.ansible.com/ui/standalone/roles/ansibleguy/sw_odoo_community)

Molecule Logs: [Short](https://badges.ansibleguy.net/log/molecule_sw_odoo_community_test_short.log), [Full](https://badges.ansibleguy.net/log/molecule_sw_odoo_community_test.log)

**Tested:**
* Debian 12

## Install

```bash
# latest
ansible-galaxy role install git+https://github.com/ansibleguy/sw_odoo_community

# from galaxy
ansible-galaxy install ansibleguy.sw_odoo_community

# or to custom role-path
ansible-galaxy install ansibleguy.sw_odoo_community --roles-path ./roles

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
    * Addons directory: `/var/local/odoo/addons`
 

  * **Default opt-ins**:
    * Install postgresql database
    * Install nginx webserver
    * Daily Database-Backup job

----

## Info

* **Note:** this role currently only supports debian-based systems


* **Note:** Most of the role's functionality can be opted in or out.

  For all available options - see the default-config located in [the main defaults-file](https://github.com/ansibleguy/sw_odoo_community/blob/latest/defaults/main/1_main.yml)!


* **Warning:** Not every setting/variable you provide will be checked for validity. Bad config might break the role!


* **Warning:** Make sure to only allow Port 80/443 to the server as odoo will listen on 8069 and optionally 8072!


* **Note:** You might want to [block login-failure using Fail2Ban](https://www.odoo.com/documentation/17.0/administration/on_premise/deploy.html#blocking-brute-force-attacks)


* **Note:** The `Master password` on the setup-screen is the `admin_pwd` set by this role. It is saved in the `/etc/odoo/odoo.conf` file.


* **Tip:** You can enhance your Odoo-community functionality using community apps. You especially might want to check out the ones provided by the [Odoo Community Association](https://github.com/OCA?q=&type=all&language=&sort=stargazers)

    To install 3th-party apps you have to:

    * Select your Odoo-Version and Download its ZIP

      <img src="https://raw.githubusercontent.com/ansibleguy/sw_odoo_community/latest/docs/app_download.png" alt="Odoo app download" width="300"/>
  
    * Move the ZIP to your server and place it inside the addons-directory (`/var/local/odoo/addons` by default in this setup) 

    * Unzip it: `cd /var/local/odoo/addons && unzip project-17.0.zip`

    * Move the modules you want/need: `mv project-17.0/project_* /var/local/odoo/addons/`

    * Clean-up: `rm -r /var/local/odoo/addons/project-17.0/`

    * Restart odoo: `systemctl restart odoo.service`


* **Note:** If you want to change to the **enterprise-edition** you will have to set `odoo.enterprise: true` so the community repository will get removed.

    Docs: [odoo Docs](https://www.odoo.com/documentation/17.0/administration/on_premise/community_to_enterprise.html#on-linux-using-an-installer)

    You have to:

    * Install the community-edition first
    * Stop the service: `systemctl stop odoo.service`
    * Download the enterprise binary `.deb` and install it as root: `dpkg -i odoo-enterprise.deb`

    NOTE: Migrating from enterprise back to community edition is not easy!


* **Tip:** You can reset your admin-user password like this:

    ```bash
    # generate hash
     export MYPWD='MY_PASSWORD'
    python3 -c "from os import environ; from passlib.context import CryptContext; print(CryptContext(['pbkdf2_sha512']).hash(environ['MYPWD']))"
    # update hash in DB
    su --login postgres
    psql
    \c odoo
    # check we have the correct account
    SELECT login, password FROM res_users WHERE id=2;
    # update it (change HASH to the value you got by running the 'python3' command above)
    UPDATE res_users SET password='HASH' WHERE id=2;    
    ```

* **Note:** You can restore the database from a dump like this:

    ```bash
    su --login postgres
  
    # make sure to backup your current state
    pg_dumpall | xz > "./odoo_$(date '+%Y-%m-%d_%H-%M').sql.xz"

    # restore
    xzcat odoo_<DATETIME>.sql.xz | psql -d odoo
    ```

    You can also manage your databases from this URL: `https://<YOUR-DOMAIN>/web/database/manager`

    Also note that the Odoo online setups might use newer versions than you can use on-premise. So you might need to contact the support on how to migrate the database.

* **Note:** The file backups are created from `/var/lib/odoo/` - this contains data like the `filestore`


* **Note:** If you want to limit outbound connections you might need to allow HTTP+S connections to these:

    ```
    nightly.odoo.com
    apps.odoo.com
    partner-autocomplete.odoo.com
    www.odoo.com
    ```

* **Note:** For PDF generation and printing to work - you will have to install [wkhtmltopdf](https://github.com/wkhtmltopdf/packaging/releases)

    You may want to check the log file if any errors occur: `tail -f /var/log/odoo/odoo-server.log`
