# Ansible WireGuard

This creates a VPN server on Ubuntu 18.04 with WireGuard.
Script is based on [Getting Started with WireGuard](https://miguelmota.com/blog/getting-started-with-wireguard/).

## Requirement

On your client computer :

- Install [WireGuard](https://www.wireguard.com/install/)
- Generate you client private and public keys :
  ```bash
  umask 077
  wg genkey | tee privatekey | wg pubkey > publickey
  ```
- Install [Ansible](https://www.ansible.com/)
- As root :
  ```bash
  ip link add dev wg0 type wireguard
  ip address add dev wg0 192.168.2.1/24
  ```

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

After running the ansible script, create the file `/etc/wireguard/wg0.conf` on your client computer and replace `<variables>` :

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
