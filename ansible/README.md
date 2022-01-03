    #########################
    #########################
    ##                     ##
    ## server_base_install ##
    ##                     ##
    #########################
    #########################

Welcome to the server_base_install README.

Use these playbooks to deploy a minimally functional, and replicable, website.

This README is _not_ a guide on using Ansible, there are lots of other resources for that and trying to include that here with brevity would likely do more of a dis-service than good.

Additionally, these roles will not work out of the box, you'll need to configure them to your specific usecase.

Instead what I hope to achieve here is a layout for automating the building blocks of a website.

Please report issues, mistakes/bugs, or suggestions via PR or email me at electronictelegrams@protonmail.com.

---

INITIAL INSTALLATION:

After you first clone this repository, before you get to fixing up all your user-definable variables you must read the roles/create_users/README.md.

That README contains important first-steps necessary to run the create_users playbook (and then therefore all subsequent playbooks).

---

***Some standards and conventions ->***

VARIABLES:

Variables are defined as globally as reasonable to avoid re-setting items over and over in each host. For more information on Ansible variable precendence, please see [this page](https://docs.ansible.com/ansible/latest/reference_appendices/general_precedence.html).

If a variable isn't configurable, but must take on multiple possible values depending on a set of conditions then do so within the task it's meant for.
- An exception to this rule are long lists that would otherwise clog up tasks. In these cases define lists in roles/<role>/vars.

**Note** In a few roles provided here the roles/<role>/vars/main.yml file is ansible-vault'ed and contains passwords. If this is strictly for private use you can probably safely keep this file plain-text, but there's really not much hassle if you encrypt it anyways. Just wanted to give a heads up here.

When setting a variable of multiple words use underscores rather than hyphens or camel-caps, i.e. apt_packages versus apt-packages or aptPackages.

---

# ***Table of Content - What to expect ->:***

Playbooks consist, broadly, of two types: 'website' and 'back-end'.

Some, but not all, roles have role-level README files for a little bit more explanation of their purpose. If something isn't clear, check there for help.

'Back-end' playbooks are responsible for server-side configuration including:
- create_dirs
- create_users
- destroy_site
- enforce_whole_site
- firewall
- gen_htpasswd
- hostname
- nginx_setup
- package_install
- repos
- sshd_config

'Website' playbooks are responsible for the content you interact with on the site, like jinja-templates, CSS, static files, etc:
- main_page

# ***PLAYBOOKS -> BACK-END:***

## create_dirs

Create the skeleton directory structure for your host, primarily all the required directories within /var/www

    ansible-playbook -i inventory -l host create_dirs.yml

## create_users

Add the system users. These can really be any names you want. This must be run with the 'ansible' user.

Please read the role-level README for initial install instructions.

    ansible-playbook -i inventory -l host create_users.yml -e "{ansible_user: ansible}"

**Note** The UID XXX3 user has a restricted shell that can _only_ run git, and, has _PasswordAuthentication yes_ in sshd.

## destroy_site

This playbook deletes the webroot directory. Eventually, it'll uninstall everything enforce_whole_site does.

    ansible-playbook -i inventory -l host destroy_site.yml

## enforce_whole_site

Does what you expect, it runs through the entire install process. Roles are idempotent after the initial install process.

    ansible-playbook -i inventory -l host enforce_whole_site.yml

## firewall

Allow ports via ufw.

    ansible-playbook -i inventory -l host firewall.yml

## gen_htpasswd

Create a password for the user 'htuser' to allow only password-authenticated users from access to the /repo path.

    ansible-playbook -i inventory -l host gen_htpasswd.yml

## hostname

Set the system hostname in /etc/hosts and /etc/hostname.

    ansible-playbook -i inventory -l host hostname.yml

## nginx_setup

Deploy the nginx configuration for your host including Certbot configurations if you have an fqdn.

    ansible-playbook -i inventory -l host nginx_setup.yml

## package_install

Install package dependencies.

    ansible-playbook -i inventory -l host package_install.yml

Optionally uninstall said packages:

    ansible-playbook -i inventory -l host package_install.yml --tags uninstall

## repos

Sets up bare repositories for your defined repos you may want to host.

    ansible-playbook -i inventory -l host repos.yml

## sshd_config

Deploy your custom sshd_config file to /etc/ssh/sshd_config.d/ as well as manage key access.

    ansible-playbook -i inventory -l host sshd_config.yml

**Note** You must put your keys in the roles/sshd_config/files/ directory before you run this, otherwise you'll get locked out.

# ***PLAYBOOKS -> WEBSITE:***

## main_page

The page you see when navigating to your host.

    ansible-playbook -i inventory -l host main_page.yml

Tags:
- push up new css: **--tags css**
- push main landing page: **--tags main**
