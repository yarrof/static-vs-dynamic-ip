# Static vs. Dynamic IP

## Story

The big boss in your workplace asked you to give him the first available IP address on the office network, because he always wants to be the first. Let's do it, or you will be fired!

## What are you going to learn?

- What is DHCP
- What is DHCP clients and servers are
- How DHCP clients and servers communicate (what are the steps of the discovery/handshake process)
- What information is shared between client and server
- What does _lease time_ mean in the context of DHCP
- What is DHCP address reservation by MAC address
- What is a DHCP _reserved address_ vs. a static address
- Know what is a dynamic IP vs. a static one
- Learn how to configure network interfaces with Netplan

## Tasks

1. Start and configure VM with bridged network settings.
    - Started a VM loaded and configured with an SSH server (either an existing one or a fresh one using [this image](https://github.com/CodecoolBase/short-admin-vms/releases/latest/download/ubuntu-18.04-base.ova))
    - The VM's Network settings are configured with a single _Bridged Adapter_
    - Determined the VM's IPv4 address for the `enp0s3` network interface (most probably received and served via the DHCP server on the local network, e.g. the home router)

2. By default most Linux distros will rely on DHCP to obtain an IP address for a network interface. When you start your VM in with a bridged network adapter it'll automatically receive an IP from a local DHCP server (most probably your own home router). Let's figure out if this is true or not by looking up your DHCP server's IP address.
    - The DHCP server's address was found and noted down

3. Disable the DHCP client in the guest VM check what's has become of its IP address by configuring Netplan's configuration YAML file in the `/etc/netplan` directory.
    - The `dchp4` property has been changed to `no` in the `.yaml` config under the `enp0s3` network interface's section and the changes were saved
    - The changes has been applied with `netplan apply`
    - The VM has no valid IPv4 address bound to the `enp0s3` network interface

4. Configure a static IP address for the VM's `enp0s3` network interface by modifying Netplan's configuration file.
    - A static IPv4 address has been added to the config for the network interface
    - The default gateway to be used by the interface has been specified
    - The changes has been saved and applied with `netplan apply`
    - The VM has has a valid IPv4 address bound to the `enp0s3` network interface (the one you've configured)
    - `ping google.com` works, meaning the VM has route to the Internet and DNS services work also

## General requirements

- `nano` editor has been used to edit the `.yaml` config file

## Hints

- Disabling DHCP in your VM will break networking and with that SSH won't work either (this is normal)
- To modify system-wide configurations like the ones under `/etc` and to use tools that have system-wide effects (like `netplan`) either login as root or use `sudo`
- You can check the IPs bound to network interfaces using `ip address` or `ip a` for short, to narrow down what you're looking for you can use `ip a | grep 'inet .*dynamic'` which will print all IPv4 addresses which were set dynamically, meaning via DHCP
- You can found your DHCP server's address using `ipconfig /all` on Windows or in your router's configuration
- When manually configuring an IP address be careful, DHCP servers dynamically select IPs for devices from a range of IPs (e.g. `192.168.0.10-192.168.0.200`)

  - If you configure an IP from this range manually (like `192.168.0.11`) there's no guarantee it's going to be unused
  - **Select your manual IP from _outside_ the DHCP range**
  - Check the address range in your DHCP server's configuration (e.g. in your router's admin page)

- **When you are reading background materials:**
  - [Wikipedia has a great image of what goes on between DHCP client and server](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol#Operation) with a more technical description
  - [How to Configure Static IP Address on Ubuntu 18.04](https://linuxize.com/post/how-to-configure-static-ip-address-on-ubuntu-18-04/)
    - The article suggests to configure a static address reservation using your MAC address in your DHCP server's config, but **don't do this for the sake of this exercise**, otherwise it's a valid idea

## Background materials

- <i class="far fa-exclamation"></i> [Static addressing with Netplan](https://netplan.io/examples/#using-dhcp-and-static-addressing)
- <i class="far fa-exclamation"></i> [_How Dynamic Host Configuration Protocol (DHCP) Works_](https://www.youtube.com/watch?v=b_9Dg0QYJUg&list=PLQVJk9oC5JKp_8F9LPa3Pv67boA80KLm1&index=13)
- [_Introduction to TCP/IP_](project/curriculum/materials/pages/networks/introduction-to-tcp-ip.md)
- [How to Configure Static IP Address on Ubuntu 18.04](https://linuxize.com/post/how-to-configure-static-ip-address-on-ubuntu-18-04/)
