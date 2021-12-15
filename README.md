# ansible-arista-backup
The start of basic Arista backups via Ansible. This will copy the running config from an EOS device to a timestamped file in the playbook location ./backups/\<System MAC\>/. A Symlink of the hostname pointing to the directory is created as well.

## Deployment Steps: 
1. ``git clone https://github.com/mgfriedel/ansible-arista-backup.git``
2. ``cd ansible-arista-backup``
3. Rename/Edit Config:
   - EOS:
     - ``cp group_vars/example-eos_devices.yml group_vars/eos_devices.yml`` and edit user, pass, etc as needed.
     - The default is to use HTTPs eAPI without SSL cert verification, which shouldn't be used in production.
     - More info/options available for Ansible httpapi here: https://docs.ansible.com/ansible/latest/collections/ansible/netcommon/httpapi_connection.html
   - CVP:
     - ``cp group_vars/example-cvp_clusters.yml group_vars/cvp_clusters.yml`` and edit pass, etc as needed.
     - SSH Keys can be used as well. Ex: ``ssh-copy-id root@<CVP Host>``
   
5. Rename example-inventory to inventory and edit IPs as needed for eos_devices and cvp_clusters groups as needed.
6. Run ``ansible-playbook -i inventory arista-backup.yml``
