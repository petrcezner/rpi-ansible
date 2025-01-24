# RPI4 Ansible Playbook

## Ansible Installation
Install Ansible and related tools:

```bash
pip install ansible ansible-lint
```

## Raspberry Pi 4 Setup
1. **Install Ubuntu 22.04 Server:**
   - Download the Raspberry Pi image from the official Ubuntu website.
   - Flash the image to an SD card using tools like `Raspberry Pi Imager` or `balenaEtcher`.
   - Boot the Raspberry Pi with the SD card.
   - During installation, select **Install OpenSSH Server**.

2. **Configure Credentials:**
   - Update the Ansible inventory file (`hosts`) with your Raspberry Pi's details:
     ```
     rpi ansible_user=<your_username>
     rpi ansible_host=<raspberry_pi_ip>
     rpi ansible_ssh_private_key_file=~/.ssh/<your_ssh_key>
     ```
   - Store your password in Ansible Vault:
     ```bash
     echo "<your_password>" > .vault_pass
     ansible-vault create host_vars/rpi/vault.yaml
     ```
     Inside the `vault.yaml` file, include:
     ```yaml
     ---
     vault_user_pass: <your_password>
     ```

3. **Generate SSH Keys:**
   - Generate a new SSH key pair if needed:
     ```bash
     ssh-keygen -t rsa -b 4096
     ```
     - Save the key pair and add the public key to the Raspberry Pi:
     ```bash
     ssh-copy-id -i ~/.ssh/<your_ssh_key> <your_username>@<raspberry_pi_ip>
     ```

4. **Verify SSH Connection:**
   Test the SSH connection:
   ```bash
   ansible rpi -m ping -u <your_username>
   ```
   Expected output:
   ```json
   rpi | SUCCESS => {
       "changed": false,
       "ping": "pong"
   }
   ```

## Ansible Configuration
- Add the Raspberry Pi's details to the `hosts` file.
- Create an `ansible.cfg` file to specify the inventory location:
  ```ini
  [defaults]
  inventory = ./hosts
  vault_password_file = ./.vault_pass
  ```

## Install Software with Ansible
Run the Ansible playbook to install necessary software:

```bash
ansible-playbook ./tasks/sites.yaml
```
or
```bash
ansible-playbook ./tasks/install_docker.yaml
ansible-playbook ./tasks/install_miniconda.yaml
ansible-playbook ./tasks/install_microk8s.yaml
```
**Note:** The installation process may take 30-40 minutes, depending on your network speed.

## Ansible Vault Usage
Ansible Vault secures sensitive information (e.g., passwords):
- Create a vault:
  ```bash
  ansible-vault create host_vars/rpi/vault.yaml
  ```
- View the vault:
  ```bash
  ansible-vault view host_vars/rpi/vault.yaml
  ```
- Edit the vault:
  ```bash
  ansible-vault edit host_vars/rpi/vault.yaml
  ```
- Automate vault password entry by configuring `ansible.cfg` as shown earlier.
  **Warning:** Never share or commit the `.vault_pass` file.

## TODO
### Setup and Install
Mandatory:
- [x] Docker (use the latest stable version)
- [x] Docker Compose (latest version)
- [x] Miniconda/Anaconda
- [x] Microk8s

