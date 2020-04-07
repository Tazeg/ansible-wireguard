# Ansible WireGuard

This creates a VPN server on Ubuntu 18.04 with WireGuard.
Script is based on [Getting Started with WireGuard](https://miguelmota.com/blog/getting-started-with-wireguard/).

## Requirement

- First of all, install [WireGuard](https://www.wireguard.com/install/) on your computer and generate you client private and public keys.
- Install [Ansible](https://www.ansible.com/) on your computer.

## Usage

The following script does the job to make for example a VPS as a VPN server. Your WireGuard client public key will be asked.

```bash
git clone https://github.com/Tazeg/ansible-wireguard.git
cd ansible-wireguard
ansible-playbook -i <IP>, playbooks/wireguard_server.yml -e "ansible_port=2222" -e "ansible_user=root"
```

- `<IP>`: replace with your Ubuntu server public IP
- `-e "ansible_port=2222"`: optional, if you are not using ssh on port 22
- `-e "ansible_user=root"`: ssh connexion as root

After running the ansible script, you can start WireGuard, create the file `/etc/wireguard/wg0.conf` and replace `<variables>` :

```ini
# local device
[Interface]
Address = 10.0.0.2/32
PrivateKey = <your client private key>
DNS = 1.1.1.1

# server
[Peer]
PublicKey = <the server public key given by ansible script>
Endpoint = <IP>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25
```

Then run on your computer :

```bash
curl https://ipinfo.io/ip # your computer public IP
sudo wg-quick up wg0
curl https://ipinfo.io/ip # you now have the public IP of the server
```

To stop connexion :

```bash
sudo wg-quick down wg0
```
