# Ansible Ngrok Installer

[Ansible](http://www.ansible.com/home) role to:
- Install Ngrok client or server as a service
- Setup firewall rules to open ports (suppports ufw only for now)

## Supported platforms

- Ubuntu 18.04 LTS (Bionic)
- Ubuntu 20.04 LTS (Focal Fossa)
- Debian 9 (Stretch)
- Debian 10 (Buster)

## Quick start

To use this as a role clone this repo inside the *role/* folder in your Ansible project.
This will install ngrok (client) and/or ngrokd (server) binaries and create a new systemd service for each of those binaries.
If you have ufw installed, it will also open the required ports in the client firewall to allow communication with the server.

You will need to provide your own ngrok and ngrokd binaries and place them in the *files* folder. 
You can follow the instructions on this [ngrok project|https://github.com/inconshreveable/ngrok] to produce the binaries.

You can also build ARM versions of the binaries and place them in a folder of your choice (e.g. *files/linux_arm*). 
You also need to set the **ngrok_arm_local_exec_path** var with the path to that folder. 
Depending on the client architecture the role will pick either the x86 or the arm binary to install.

You need to select which role you are going to install (client or server) by setting the **ngrok_server** and **ngrok_client** vars.
By default both vars are set to false, so nothing is installed if you run the role without setting at least one of these vars.

### Example Playbooks

To setup a server that exposes services under the *some.domain* domain:

```yml
---
- hosts: myserver
  become: true
  vars:
    ngrok_server: yes
    ngrok_server_addr: some.domain
```

To setup a client to expose a webserver and ssh to the ngrok server under the *some.domain* URLs:

```yml
---
- hosts: myhost
  become: true
  vars:
    ngrok_client: yes
    ngrok_server_addr: some.domain
    ngrok_tunnels:
        - name: somewebserver
            port: 8080
            protocol: http
        - name: myhostssh
            remote_port: 8022
            port: 22
            protocol: tcp

```

In this case the webserver running locally on port 8080 (local) will be accessible from somewebserver.some.domain. The ssh will be accessible from myhostssh.some.domain on port 8022.

## Default role variables

### Server domain

You can set the server domain with the **ngrok_server_addr** var. This needs to be set both on the client and the server. All tunnel URLs will be created from this domain.

```yml
ngrok_server_addr: some.domain

```

### Tunnel definition



### Firewall

Sets firewall rules depending on what is set on **ngrok_tunnels** - only ufw is supported for now
If remote ports are defined they need to be open in the ngrok client so that communication from the outside is possible. 
You can restrict the IP addresses that access those remote ports by setting the **ngrok_allowed_ips** var