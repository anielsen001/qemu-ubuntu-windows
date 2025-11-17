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

```
sudo systemctl enable --now libvirtd
sudo systemctl status libvirtd
```

launch the virtual manager

```
virt-manager
```

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
