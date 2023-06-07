# WireGuard VPN

WireGuard is a fast and modern VPN protocol that provides strong security and high performance. It uses advanced cryptography to ensure your online privacy and protects your data from eavesdropping, tampering, and other cyber threats.

## How WireGuard Works

WireGuard works by creating a secure and encrypted tunnel between two devices. It uses state-of-the-art cryptography to ensure the CIA (confidentiality, integrity and availability) of the data transmitted over the network.

WireGuard is designed to be lightweight, efficient, and easy to use. It uses a simple and elegant design that makes it easy to set up and configure. It is also highly scalable, which means it can be used in a wide range of scenarios, from small personal networks to large enterprise environments.

## Benefits of WireGuard

WireGuard has many benefits, including:

- Strong security: WireGuard uses advanced cryptography to protect your data from eavesdropping, tampering, and other cyber threats.

- High performance: WireGuard is designed to be fast and efficient, which means you can enjoy high-speed VPN connections without sacrificing security.

- Easy to use: WireGuard has a simple and elegant design that makes it easy to set up and configure.

- Cross-platform compatibility: WireGuard works on a wide range of operating systems, including Windows, macOS, Linux, iOS, and Android.

- Open-source: WireGuard is an open-source project, which means it is transparent and can be audited by anyone.

## Use of Wireguard in the project

In this project we will be using Wireguard as a backup for when the ssh tunnels fail. When they fail, we will still be able to reach the gateways thanks to the Wireguard VPN tunnels.

We will also use Wireguard to route the MQTT traffic since this traffic is not encrypted by default. As mentioned before, Wireguard provides strong encryption thanks to state-of-the-art encryption.

## Wireguard server setup

In this project we use a self-made Ansible role to set up the Wireguard server. We set up the clients (gateways) using a bash script, but more on that later.

Here is the Ansible role that sets up the Wireguard server:

```yaml
---
# tasks file for ansible/roles/wireguard-setup 

# this task installs Wireguard on the server.
- name: Install wireguard
  apt:
    pkg: wireguard
  become: true

# this task generates the private server key used by Wireguard.
- name: Generate private server key
  shell: "wg genkey > /etc/wireguard/private_server.key"
  become: true

# this task generates the public server key used by Wireguard.
- name: Generate public server key
  shell: "cat /etc/wireguard/private_server.key | wg pubkey > /etc/wireguard/public_server.key"
  become: true
  
# this task generates the private key of the first client used by Wireguard.
- name: Generate private client1 key
  shell: "wg genkey > /etc/wireguard/private_client1.key"
  become: true

# this task generates the public key of the first client used by Wireguard.
- name: Generate public client1 key
  shell: "cat /etc/wireguard/private_client1.key | wg pubkey > /etc/wireguard/public_client1.key"
  become: true

# this task generates the private key of the second client used by Wireguard.
- name: Generate private client2 key
  shell: "wg genkey > /etc/wireguard/private_client2.key"
  become: true

# this task generates the public key of the second client used by Wireguard.
- name: Generate public client2 key
  shell: "cat /etc/wireguard/private_client2.key | wg pubkey > /etc/wireguard/public_client2.key"
  become: true

# this task gets the private server key and assigns it to a variable.
- name: Get private server key
  shell: cat /etc/wireguard/private_server.key
  register: private_key_server
  become: true

# this task gets the private key of the first client and assigns it to a variable.
- name: Get private client1 key 
  shell: cat /etc/wireguard/private_client1.key
  register: private_key_client1
  become: true
  
# this task gets the private key of the second client and assigns it to a variable.
- name: Get private client2 key 
  shell: cat /etc/wireguard/private_client2.key
  register: private_key_client2
  become: true

# this task gets the public key of the first client and assigns it to a variable.
- name: Get public client1 key 
  shell: cat /etc/wireguard/public_client1.key
  register: public_key_client1
  become: true
  
# this task gets the public key of the second client and assigns it to a variable.
- name: Get public client2 key 
  shell: cat /etc/wireguard/public_client2.key
  register: public_key_client2
  become: true

# This task copies the template server configuration to the server.
- name: Copy local server config file to remote machine
  ansible.builtin.copy:
    src: files/wg0-server.conf
    dest: /etc/wireguard/wg0.conf
    mode: preserve
  become: true
 
# this task inserts the private server key into the server configuration file.
- name: Insert private key into server config file
  lineinfile:
    path: /etc/wireguard/wg0.conf
    regexp: '^PrivateKey ='
    line: 'PrivateKey = {{ private_key_server.stdout }}'
    backrefs: yes
  become: true 
  
# this task inserts the Wireguard server address into the server configuration file.
- name: Insert wireguard server address into server config file
  lineinfile:
    path: /etc/wireguard/wg0.conf
    regexp: '^Address ='
    line: 'Address = {{ wireguard_server_address }}'
    backrefs: yes
  become: true

# this task inserts the public key of the first client into the server configuration file.
- name: Insert public client1 key into server config file
  lineinfile:
    path: /etc/wireguard/wg0.conf
    regexp: '^# Gateway 1$'
    insertbefore: '^# Gateway 2$'
    line: |
      [Peer]
      PublicKey = {{ public_key_client1.stdout }}
      AllowedIPs = {{ allowed_ips1 }}
  become: true

# this task inserts the public key of the second client into the server configuration file.
- name: Insert public client2 key into server config file
  lineinfile:
    path: /etc/wireguard/wg0.conf
    regexp: '^# Gateway 2$'
    line: |
      [Peer]
      PublicKey = {{ public_key_client2.stdout }}
      AllowedIPs = {{ allowed_ips2 }}
  become: true

# this task inserts the listen port into the server configuration file.
- name: Insert listen port into server config file
  lineinfile:
    path: /etc/wireguard/wg0.conf
    regexp: '^ListenPort ='
    line: 'ListenPort = {{ listen_port }}'
    backrefs: yes
  become: true

# this task starts the Wireguard service.
- name: Start wireguard service
  systemd:
    name: wg-quick@wg0
    enabled: yes
    state: started
  become: true

# this task adds the public client keys to the server.
- name: Add client public key to server
  shell: wg set wg0 peer {{ public_key_client1.stdout }} allowed-ips {{ allowed_ips1 }} && wg set wg0 peer {{ public_key_client2.stdout }} allowed-ips {{ allowed_ips2 }}
  become: true
```

Here is an example Ansbile playbook that uses this role:
```yaml
- name: Wireguard setup
  vars:
    wireguard_server_address: "10.10.10.1/24"
    allowed_ips1: "10.10.10.2/32"
    allowed_ips2: "10.10.10.3/32"
    listen_port: 51820

  roles:
    - role: wireguard-setup
```
If you use these variables, the server configuration would look something like this:
```conf
[Interface]
PrivateKey = <private server key>
Address = 10.10.10.1/24
ListenPort = 51820
SaveConfig = true

[Peer]
PublicKey = <public client 1 key>
AllowedIPs = 10.10.10.2/32

[Peer]
PublicKey = <public client 2 key>
AllowedIPs = 10.10.10.3/32
```
The template configuration files can be found in the '**/ansible/roles/files**' folder

## Wireguard client setup

This part of the bash script sets up a Wireguard client on a Debian-based system. It installs the Wireguard package and generates a configuration file for the client that connects to a Wireguard server running at 94.130.179.123 with a specified private key and public key.

### Configuration

The script sets up the following variables for configuration:

- `server_address`: The IP address of the Wireguard server.
- `client_address`: The IP address for the client on the Wireguard network.
- `allowed_ips`: The IP address range allowed for communication through the Wireguard connection.

### Script

Here we see a part of the script that sets up the gateways. This part in particular sets up the Wireguard client for 1 client. When we want to setup the second one, we need to make a few changes to the script. The client address and the private client key can't be the same for both so they need changing.

```bash
#!/bin/bash

# Configure variables
server_address=94.130.179.123
client_address=10.10.10.2
allowed_ips="10.10.10.0/24"

# Update repositories and install Wireguard
apt update
apt install wireguard -y

# Retrieve private client key and public server key
private_client_key=$(ssh flwsb@$server_address "sudo cat /etc/wireguard/private_client1.key")
public_server_key=$(ssh flwsb@$server_address "sudo cat /etc/wireguard/public_server.key")

# Generate client configuration file
echo "# Example config for the Wireguard client, works fine with the server config
[Interface]
PrivateKey = $private_client_key
Address = $client_address

[Peer]
PublicKey = $public_server_key
AllowedIPs = $allowed_ips
Endpoint = $server_address:51820
PersistentKeepalive = 25" | tee /etc/wireguard/wg0.conf

# Start Wireguard connection
wg-quick up wg0
```

After running the script, the user should manually ping the Wireguard server at 10.10.10.1 to confirm the successful setup of the Wireguard client. You can do this like so:
```
ping 10.10.10.1
```

Note: The script assumes that the private client key and public server key are located at /etc/wireguard/private_client1.key and /etc/wireguard/public_server.key, respectively. For multiple gateways, the script can be modified to retrieve the keys from the appropriate file locations. This bash script obviously also needs '**sudo**' priviliges to run correctly.

If this works, the Wireguard has been set up succesfully. For more information about this topic, click [here](https://www.digitalocean.com/community/tutorials/how-to-set-up-wireguard-on-ubuntu-22-04).
