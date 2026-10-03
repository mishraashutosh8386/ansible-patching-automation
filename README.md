# Ansible Patching Automation (Linux and Windows)

Playbooks that automate OS patching for mixed Linux and Windows servers,
replacing manual, server-by-server updates with a repeatable process.

## What it does
- **Linux (`patch_linux.yml`):** upgrades all packages on Debian/Ubuntu and RHEL-family hosts, one server at a time (`serial: 1`), and reboots only if the OS reports a reboot is required.
- **Windows (`patch_windows.yml`):** installs security, critical and rollup updates with `win_updates` and reboots when needed, then prints a summary of installed updates.

## Project structure
```
.
├── inventory.ini        # linux and windows host groups (placeholders)
├── patch_linux.yml
├── patch_windows.yml
└── README.md
```

## Requirements
- Ansible 2.14+ on the control node
- Collections: `ansible-galaxy collection install ansible.windows`
- For Windows targets: `pip install pywinrm` and WinRM enabled on the hosts
- SSH access to Linux hosts; credentials stored with `ansible-vault`, not in Git

## Usage
```bash
# Dry run first
ansible-playbook -i inventory.ini patch_linux.yml --check

# Patch Linux servers
ansible-playbook -i inventory.ini patch_linux.yml

# Patch Windows servers
ansible-playbook -i inventory.ini patch_windows.yml --ask-vault-pass
```

## Tested on
- Azure Virtual Machines
- 3 x Linux (Ubuntu 22.04) test servers
- 2 x Windows test servers

## Results
- Linux playbook patched all 3 servers one at a time and rebooted only where required.
- Windows playbook installed pending security and critical updates on both servers and rebooted them.
- Ran with no failed hosts (`failed=0` in the Ansible play recap).
  
## Possible improvements
- Schedule runs through Ansible Automation Platform job templates
- Add pre-patch snapshot and post-patch health checks
- Add patch windows and email/Teams notification on failure
