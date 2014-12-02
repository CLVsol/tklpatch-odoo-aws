tklpatch-odoo-aws
=================

This project will help you install `Odoo <https://www.odoo.com/>`_ over a `TurnKey LAMP <http://www.turnkeylinux.org/lamp>`_ appliance, using the Amazon Web Services (AWS) EC2 infrastructure.

#. Create, via TrunKey Hub, an Amazon EC2 instance:

	- TurnKey Appliance: **LAMP Stack (13.0)**
	- Hostname: **[tkl-odoo-aws]**
	- Region: **[Sao Paulo (South America)]**
	- Type: **EBS-backed (persistent)**
	- Architecture: **64bit (amd64)**
	- Size: **[Micro ($0.027/hour)]**
	- SSH key-pair: **[tkl-odoo-aws]**
	- Root file system size (GB): **[10]**

	Related information:

	- Security Groups: **[turnkey-lamp-xxxx]**
	- Private Key File: **[tkl-odoo-aws.pem]**

	Security Group: [turnkey-lamp-xxxx] (Inbound)::

		Port (Service)   Source
		-------------------------------------
		N/A(PING)        0.0.0.0/0
		22(SSH)          0.0.0.0/0
		80(HTTP)         0.0.0.0/0
		443(HTTPS)       0.0.0.0/0
		12320(Web Shell) 0.0.0.0/0  (disable)
		12321(Webmin)    0.0.0.0/0  (disable)
		12322(Custom)    0.0.0.0/0  (disable)

#. `Disable Password-based Login <http://aws.amazon.com/articles/1233?_encoding=UTF8&jiveRedirect=1>`_

	Log in to your instance as root and edit the ssh daemon configuration file "**/etc/ssh/sshd_config**"

	Find the line::

		PasswordAuthentication yes

	and change it to::

		PasswordAuthentication no

	Save the file and restart sshd::

		/etc/init.d/ssh restart

	**You will now only be able to log in with an ssh key.**

#. Update host name, executing the following commands::

		HOSTNAME=tkl-odoo-aws
		echo "$HOSTNAME" > /etc/hostname
		sed -i "s|127.0.1.1 \(.*\)|127.0.1.1 $HOSTNAME|" /etc/hosts
		/etc/init.d/hostname.sh start

#. Upgrade the software::

		apt-get update
		apt-get -y upgrade

#. Install **TKLPatch** using the commands::

	apt-get update
	apt-get -y install tklpatch

	The documentation is installed at "/usr/share/tklpatch/docs" and the exemples at "/usr/share/tklpatch/docs".

#. Get the TKLPatch script "**clvsol_tklpatch-odoo-aws**" using the commands::

	cd /root
	git-clone https://github.com/CLVsol/tklpatch-odoo-aws.git clvsol_tklpatch-odoo-aws

#. Apply the patch "clvsol_tklpatch-odoo-aws"::

	cd /root
	tklpatch-apply / clvsol_tklpatch-odoo-aws

#. Change manually, using Webmin, the passwords for the accounts::

	root (Linux)
	root (MySQL)
	postgres (PostgreSQL)
	openerp (Linux)
	openuser (PostgreSQL)

#. Change manually, editing the Odoo configuration files (/opt/openerp/odoo/**openerp-server.conf**, /opt/openerp/odoo/**openerp-server_man.conf**), the passwords for the accounts::

	admin (Odoo server - admin_passwd)
	openuser (account on PostgreSQL - db_password)

#. Reboot the **tkl-odoo-aws** instance::

	reboot

#. Update the Security Group::

	Security Group: [turnkey-lamp-xxxx] (Inbound)::

		Port (Service)   Source
		-------------------------------------
		N/A(PING)        0.0.0.0/0
		22(SSH)          0.0.0.0/0
		80(HTTP)         0.0.0.0/0
		443(HTTPS)       0.0.0.0/0
		8069(Custom)     0.0.0.0/0  (disable)
		12320(Web Shell) 0.0.0.0/0  (disable)
		12321(Webmin)    0.0.0.0/0  (disable)
		12322(Custom)    0.0.0.0/0  (disable)
		12323(Custom)    0.0.0.0/0  (disable)
		12325(Custom)    0.0.0.0/0

#. To stop and start the Odoo server, use the following commands (as root)::

	/opt/openerp/openerp.init stop

	/opt/openerp/openerp.init start

#. Especial commands::

	git remote add origin https://github.com/CLVsol/tklpatch-odoo-aws.git
	git push -u origin master

.. toctree::
    :maxdepth: 2
