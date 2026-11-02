# Home Lab: Ubuntu Server and Nginx on Apple Silicon

## Overview

Built a Linux virtual machine on an everyday M1 Mac and installed Nginx to serve a custom HTML page. Verified access from a browser on the Mac.

Completed on November 2, 2026 with guided assistance. This is a personal learning project.

## Environment

| Component | Configuration |
| --- | --- |
| Host | Apple M1 Mac, 8 GB RAM, macOS Sequoia 15.6 |
| Hypervisor application | UTM 5.0.8, QEMU with hardware virtualization |
| Guest | Ubuntu Server 24.04.5 LTS, ARM64 |
| VM name | Home Lab - Ubuntu Server |
| Hostname | lab-web-01 |
| CPU / memory | 2 cores / 2 GB RAM |
| Virtual disk | 25 GB, guided LVM partitioning |
| Network | UTM Shared Network, DHCP |
| Web server | Nginx from Ubuntu packages |

## Architecture

```text
M1 Mac running macOS
  ├── Browser → HTTP port 80 → Ubuntu VM
  └── UTM
       └── lab-web-01
            ├── Ubuntu Server ARM64
            └── Nginx → /var/www/html/index.html
```

The VM received `192.168.64.2` during this build. DHCP addresses may change. The website is a local lab service; it has not been published to the Internet.

## Build steps

1. Downloaded UTM from its official website and installed it in Applications.
2. Downloaded the Ubuntu Server ARM64 ISO from Ubuntu and checked its SHA-256 checksum against the published SHA256SUMS file. The check passed.
3. Created a Linux VM using Virtualize, with 2 cores, 2048 MiB memory, and a 25 GB virtual disk.
4. Attached the Ubuntu ISO. Left shared directories unconfigured.
5. Installed standard Ubuntu Server with the default English keyboard layout, DHCP networking, no proxy, and the tested Ubuntu package mirror.
6. Used guided LVM partitioning on the new virtual disk. The installer initially allocated approximately 11 GB to the root logical volume, leaving space available in the volume group.
7. Created a local administrator account and set its password directly in the installer.
8. Skipped Ubuntu Pro, OpenSSH server, and featured snaps.
9. Allowed installation and security updates to finish, ejected the ISO, and rebooted.
10. Logged in and installed Nginx:

```bash
sudo apt install -y nginx
```

11. Opened `http://192.168.64.2/` from the Mac and verified the default Welcome to nginx page.
12. Created a custom homepage at `/var/www/html/index.html` using the HTML included in this repository.
13. Retrieved the custom homepage from the Mac using HTTP and compared the returned HTML to `index.html`. The files matched exactly.

## Deploy the included page

Copy `index.html` into the VM, then run this inside Ubuntu from the directory containing the file:

```bash
sudo cp index.html /var/www/html/index.html
```

Refresh the lab URL in the Mac browser.

## Useful checks to practice

These are commands to run when learning or troubleshooting; they are not a claim that every check below was executed during this build.

```bash
hostname
hostname -I
systemctl is-active nginx
systemctl is-enabled nginx
sudo nginx -t
curl -I http://localhost
sudo tail -n 20 /var/log/nginx/access.log
sudo tail -n 20 /var/log/nginx/error.log
```

## Troubleshooting notes

- The server-name field received repeated characters during automated keyboard entry. Correcting it to `lab-web-01` allowed account setup to continue.
- After installation, Ubuntu requested removal of installation media. Ejected the ISO using UTM's Virtual Machine → Drives menu, then pressed Enter.
- Password entry is invisible in the Linux console. Type the password once and press Enter.
- If the site stops responding, check that the VM is running, confirm its current IP with `hostname -I`, and check Nginx with `systemctl is-active nginx`.

## Daily use

Start the VM from UTM and open its current IP in your browser. To shut down cleanly, run `sudo poweroff` inside Ubuntu. The site is unavailable while the VM is off or the Mac is asleep.

## Skills practiced

- Creating and sizing an ARM64 virtual machine
- Installing and administering Ubuntu Server
- Understanding DHCP and private IP addressing
- Installing packages with apt and using sudo
- Hosting HTML with Nginx
- Testing a guest service from the host browser
- Recording setup and troubleshooting steps

## Remaining work

- Practice the service and log checks above and record their results.
- Add a Windows Server / Active Directory lab separately; it is not implemented in this project yet.

## Resume wording

“Built an Ubuntu Server home lab on Apple Silicon using UTM; deployed Nginx, hosted a custom HTML page, and verified HTTP access from the host computer.”

Use this wording after reviewing and understanding the build steps. Do not claim Active Directory or Windows Server experience from this Linux-only milestone.

## References

- [UTM](https://getutm.app/)
- [Ubuntu ARM64 installer directory](https://cdimages.ubuntu.com/ubuntu/releases/24.04.5/release/)
- [Nginx documentation](https://nginx.org/en/docs/)
