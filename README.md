# Base VPS Setup

Generic VPS hardening - OS agnostic baseline for all new servers.

## What it does

- Creates admin user with SSH key authentication
- Migrates SSH to custom port (default: 2244)
- Disables root login and password authentication  
- Configures UFW firewall (ports: SSH custom, 80, 443)
- Installs and configures fail2ban
- Uses dual-port SSH transition to prevent lockout

## Usage

1. Copy inventory example:
```bash
cp inventory.ini.example inventory.ini
```

2. Edit `inventory.ini` with your server details:
```ini
[servers]
123.45.67.89 ansible_user=root ansible_port=22 ansible_ssh_private_key_file=~/.ssh/id_rsa

[servers:vars]
admin_user=youruser
ssh_port=2244
ssh_key_path=~/.ssh/id_rsa.pub
```

3. Run playbook:
```bash
ansible-playbook -i inventory.ini playbooks/setup.yml
```

4. After completion, SSH using:
```bash
ssh -p 2244 youruser@123.45.67.89
```

## Safety Features

- **Dual-port SSH transition**: Listens on both 22 and custom port temporarily
- **Disables ssh.socket**: Prevents systemd from overriding port config
- **UFW timing**: Firewall enabled only AFTER SSH verified working
- **Idempotent**: Safe to re-run multiple times

## Requirements

- Fresh Ubuntu/Debian VPS
- Root SSH access on port 22
- SSH key pair generated locally
