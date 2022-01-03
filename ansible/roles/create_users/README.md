## create_users

Add the system users. These can really be any names you want. This must be run with the 'ansible' user.

Prior to running this role the 'ansible' user must first exist. This should be done upon first creation of your server using the following:

    # groupadd -g 950 950
    # useradd -m -G sudo -u 950 -g 950 ansible
    # echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
    # passwd ansible

Make sure your password for the ansible user is very strong.

### Variables
Users (vaulted in host_vars/your_host/vault.yml):
- admin_user (uid 1001)
- ancillary user (uid 1000)
- repo_user (uid 1003)

Passwords (vaulted in roles/create_users/vars/main.yml):
- admin_pass
- ancillary_user_pass
- repo_user_pass


**NOTE FOR INITIAL INSTALLS**

On initial setup of your server, this must be the first role you run. Included below are initial install instructions and where you must run the commands.

### on your local machine

    export IP=yourServersIP
    ssh root@"$IP"

### on your server

    # Change your root password. I recommend using `pass`, but use whatever password manager you typically use.
    passwd # change it to something VERY strong

    # Add the 'ansible' user.
    groupadd -g 950 950
    useradd -m -G sudo -u 950 -g 950 ansible
    echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

    passwd ansible # change it to something VERY strong

    exit

### on your local machine

    ssh-copy-id ansible@"$IP"

    ansible-playbook -i inventory -l your_host create_users.yml -e "{ansible_user: ansible}" --ask-vault-pass

    ssh-copy-id your_admin_user@"$IP"

    ansible-playbook -i inventory -l your_host enforce_whole_site.yml --ask-vault-pass
