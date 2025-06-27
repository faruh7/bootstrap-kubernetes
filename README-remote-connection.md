# Remote Linux Server Connection with Ansible

This directory contains Ansible playbooks and configuration files for connecting to and configuring remote Linux servers, particularly in preparation for Kubernetes deployment.

## Files Overview

- `connect-remote-server.yml` - Main Ansible playbook for server connection and basic setup
- `inventory.ini` - Inventory file defining target servers
- `ansible.cfg` - Ansible configuration file with optimized settings

## Prerequisites

1. **Ansible Installation**
   ```bash
   # Ubuntu/Debian
   sudo apt update && sudo apt install ansible -y
   
   # CentOS/RHEL
   sudo yum install epel-release -y && sudo yum install ansible -y
   
   # MacOS
   brew install ansible
   ```

2. **SSH Key Setup**
   ```bash
   # Generate SSH key if you don't have one
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   
   # Copy public key to remote servers
   ssh-copy-id user@remote_server_ip
   ```

## Configuration

1. **Update Inventory File**
   Edit `inventory.ini` and replace the example entries with your actual server details:
   ```ini
   [remote_servers]
   my-server ansible_host=YOUR_SERVER_IP ansible_user=YOUR_USERNAME ansible_ssh_private_key_file=~/.ssh/id_rsa
   ```

2. **Verify Connectivity**
   ```bash
   # Test connection to all servers
   ansible all -m ping
   
   # Test connection to specific group
   ansible remote_servers -m ping
   ```

## Usage

### Basic Server Connection and Setup
```bash
# Run the main playbook
ansible-playbook connect-remote-server.yml

# Run on specific hosts
ansible-playbook connect-remote-server.yml --limit server1

# Run with verbose output
ansible-playbook connect-remote-server.yml -v
```

### Advanced Usage
```bash
# Check what would be changed (dry-run)
ansible-playbook connect-remote-server.yml --check

# Run specific tags only
ansible-playbook connect-remote-server.yml --tags "setup,security"

# Skip specific tags
ansible-playbook connect-remote-server.yml --skip-tags "firewall"

# Run with custom variables
ansible-playbook connect-remote-server.yml -e "remote_user=admin"
```

## What the Playbook Does

The `connect-remote-server.yml` playbook performs the following tasks:

1. **Connection Testing**
   - Tests connectivity to remote servers
   - Gathers system information

2. **System Information**
   - Displays OS, kernel, architecture details
   - Shows CPU, memory, and uptime information

3. **Package Management**
   - Updates package cache
   - Installs essential packages (curl, wget, vim, htop, git, etc.)

4. **Security Configuration**
   - Configures SSH security settings
   - Sets up firewall rules for Kubernetes ports
   - Disables root login and password authentication

5. **Tool Detection**
   - Checks for Docker installation
   - Verifies Kubernetes tools (kubectl, kubeadm, kubelet)

6. **User Management**
   - Creates remote user if needed
   - Sets up SSH key authentication

## Customization

### Variables
You can customize the playbook behavior by modifying variables:

```yaml
vars:
  remote_user: "ubuntu"  # Change default user
  ssh_key_path: "~/.ssh/id_rsa"  # SSH key path
```

### Adding Custom Tasks
Add your own tasks to the playbook as needed:

```yaml
- name: Your custom task
  command: your_command_here
```

## Troubleshooting

### Common Issues

1. **Connection refused**
   - Verify server IP and SSH port
   - Check firewall settings
   - Ensure SSH service is running

2. **Permission denied**
   - Verify SSH key is correct
   - Check user permissions
   - Ensure public key is in authorized_keys

3. **Host key verification failed**
   - The playbook disables host key checking
   - Or manually add host key: `ssh-keyscan -H server_ip >> ~/.ssh/known_hosts`

### Debug Commands
```bash
# Test SSH connection manually
ssh -i ~/.ssh/id_rsa user@server_ip

# Run Ansible with maximum verbosity
ansible-playbook connect-remote-server.yml -vvvv

# Check inventory
ansible-inventory --list
```

## Security Notes

- The playbook configures SSH security by disabling root login and password authentication
- Firewall rules are set up for Kubernetes-specific ports
- Always review and customize security settings for your environment
- Use strong SSH keys and keep them secure

## Next Steps

After successfully connecting to your remote servers, you can:

1. Deploy Docker and container runtime
2. Install Kubernetes components
3. Initialize Kubernetes cluster
4. Deploy applications

This playbook provides a solid foundation for remote server management and Kubernetes deployment preparation.
