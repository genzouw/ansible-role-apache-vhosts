ansible-role-apache-vhosts
======

Ansible role to create lots of apache vhosts, with jailed sftp users.

**Note: Works with EL6/7(?), Debian 6/7, Ubuntu 14. For now, a2ensite breaks on Ubuntu 12 due to vhost config filenames.**

Requirements
------------
- Apache up and running
- sshd_config configured for jailing (note: restart ssh after running role so group is present):
```bash
    Subsystem sftp internal-sftp

    Match Group sftponly
	    ChrootDirectory %h
	    AllowTcpForwarding no
	    X11Forwarding no
	    ForceCommand internal-sftp
```

Example playbook
----------------
```yaml
    ---
    - hosts: web
      vars_files:
        - vhosts/vars/main.yml
        - vhosts/vars/{{ ansible_os_family }}.yml
      roles:
        - vhosts
```

Where vars/main.yml contains:
```yaml
   ---
   apache_group: 'apache'
   vhosts:
       - { server_name: 'site1.com', vhost_home: '/var/www/vhosts/site1.com', vhost_owner: 'site1' }
       - { server_name: 'site2.com', vhost_home: '/var/www/vhosts/site2.com', vhost_owner: 'site2' }
```

You can specify as many sites as required.

Password hashing
----------------

Note that the 'sftp_pass' variable takes a hash that is inserted directly into /etc/shadow.
You can use:
```bash
python -c "from passlib.hash import sha512_crypt; print sha512_crypt.encrypt('password')"
```

Todo
----
- Need to add support for Ubuntu versions < 14
- Build in password hashing
