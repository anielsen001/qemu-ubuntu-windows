https://sysguides.com/install-a-windows-11-virtual-machine-on-kvm

```
sudo apt update
sudo apt upgrade
```

check if system supports virtualization, install `cpu-checker` 

```bash
sudo apt install cpu-checker
```

then run `kvm-ok` should see results like this if all is ok. otherwise, may need to turn on virtualization option in the bios.
```bash
$ kvm-ok
INFO: /dev/kvm exists
KVM acceleration can be used
```

Install qemu/kvm toools
```
sudo apt install qemu-system qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager
```

Make sure your user is in the appropriate groups - for me these have been taken care of when installing the qemu/kvm tools.
```
sudo adduser $USER libvirt
sudo adduser $USER kvm
```

Check the status of the daemon
```bash
sudo systemctl status libvirtd
● libvirtd.service - libvirt legacy monolithic daemon
     Loaded: loaded (/usr/lib/systemd/system/libvirtd.service; enabled; preset:>
     Active: active (running) since Mon 2025-11-17 09:13:45 EST; 1min 42s ago
TriggeredBy: ● libvirtd.socket
             ● libvirtd-admin.socket
             ● libvirtd-ro.socket
       Docs: man:libvirtd(8)
             https://libvirt.org/
   Main PID: 1882 (libvirtd)
      Tasks: 22 (limit: 32768)
     Memory: 19.7M (peak: 23.6M)
        CPU: 374ms
     CGroup: /system.slice/libvirtd.service
             ├─1882 /usr/sbin/libvirtd --timeout 120
             ├─1993 /usr/sbin/dnsmasq --conf-file=/var/lib/libvirt/dnsmasq/defa>
             └─1994 /usr/sbin/dnsmasq --conf-file=/var/lib/libvirt/dnsmasq/defa>

Nov 17 09:13:45 MagNav systemd[1]: Started libvirtd.service - libvirt legacy mo>
Nov 17 09:13:46 MagNav dnsmasq[1993]: started, version 2.90 cachesize 150
Nov 17 09:13:46 MagNav dnsmasq[1993]: compile time options: IPv6 GNU-getopt DBu>
Nov 17 09:13:46 MagNav dnsmasq-dhcp[1993]: DHCP, IP range 192.168.122.2 -- 192.>
Nov 17 09:13:46 MagNav dnsmasq-dhcp[1993]: DHCP, sockets bound exclusively to i>
Nov 17 09:13:46 MagNav dnsmasq[1993]: reading /etc/resolv.conf
```

If it's not running enable it
```bash
sudo systemctl enable --now libvirtd
sudo systemctl status libvirtd
```

launch the virtual manager

```
virt-manager
```

## Install windows

### Download windows iso

go to <https://www.microsoft.com/en-us/software-download/windows11> and look for the section called 
"Download Windows 11 Disk Image (ISO) for x64 devices". You'll have to select the iso option (only presented with one) and then answer some questions about which language support you prefer. The download will start, it's about 8 GB.

### Download virtio drivers

- <https://www.linux-kvm.org/page/WindowsGuestDrivers/Download_Drivers>
- <https://github.com/virtio-win/virtio-win-pkg-scripts/blob/master/README.md>
- Download link: <https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso>

### Create VM

Start virtual machine manager
```bash
virt-manager
```
In the GUI select `File->New Virtual Machine`

- Add a second CD-ROM and insert the virtio drivers iso image. You may need this to install drivers so that windows 11 can see the virtual hdd.
- Give it 16384 MB RAM

When the system starts to boot, and talks about pressing any key to boot from CD-ROM hit a key. If waiting too long, the system may not boot and go to bios instead. 

you can get the cursur control back with `CTRL-L + ATL-L`

To bypass windows password/account

```
Use Shift + F10 and then digit "OOBE\BYPASSNRO" (without quotation mark). Doing that, the PC will reboot and allow you to configure local account without internet. I have tried that when I'm installing Windows 11 on my custom built desktop PC and it worked.

Oh, and if you are already in account setup screen, you can digit invalid email and password, so the OOBE will allow you to create local account.
```

https://www.reddit.com/r/windows/comments/11r9q8o/windows_11_inital_setup_skip_microsoft_account/

do shoft-f10 and
```
start ms-cxh:localonly
```
https://www.tomshardware.com/how-to/install-windows-11-without-microsoft-account

## sharing drives from host

install on host
```bash
sudo apt install virtiofsd
```
The `virtiofsd` command is installed into `/usr/libexec/virtiofsd` which is not on the root path.

enable shrared memory with host

https://linuxconfig.org/how-to-mount-a-host-directory-inside-a-kvm-virtual-machine
https://shallowsky.com/blog/linux/qemu-shared-folder.html
https://www.debugpoint.com/kvm-share-folder-windows-guest/
https://virtio-fs.gitlab.io/howto-windows.html

### install winfsp into guest

https://winfsp.dev/rel/
