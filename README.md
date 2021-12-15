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
     - If external SSL/SSH certificates/keys are in use, they will need to be archived outside of this playbook as some are not allowed to be pulled off the system for security reasons.
   - CVP:
     - ``cp group_vars/example-cvp_clusters.yml group_vars/cvp_clusters.yml`` and edit pass, etc as needed.
     - SSH Keys can be used as well. Ex: ``ssh-copy-id root@<CVP Host>``
   
5. Rename example-inventory to inventory and edit IPs as needed for eos_devices and cvp_clusters groups as needed.
6. If there are a lot of unused/old EOS images stored in CVP, it's recommended to remove them to save space. After they're removed, a new backup can be triggered with: ``cvpi backup cvp``
7. Run ``ansible-playbook -i inventory arista-backup.yml``

## Recovery:
- EOS:
   - Copy the running-config backup file to the systems flash (/mnt/flash via shell), USB stick, or remotely accessible location.
   - Copy to switch running-config:
     - Flash: ``copy flash:/<file> running-config``
     - USB: ``copy usb1:/<file> running-config``
     - Remote: ``copy <proto>:/<file> running-config`` where proto could be scp|sftp|http|https|tftp|ftp.
- CVP:
  - Copy the cvp.<date>.tgz and cvp.eosimages.<date> files to /data/cvpbackup on the primary CVP host as root.
  - Run ``cvpi restore cvp cvp.<date>.tgz cvp.eosimages.<date>.tgz`` on the primary CVP host as root.
  - More CVP backup/restore info available here: https://www.arista.com/en/cg-cv/cv-backup-and-restore
