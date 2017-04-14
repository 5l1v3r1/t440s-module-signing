# t440s-module-signing
A shell script to bootstrap kernel module signing when running a computer using UEFI boot (for VirtualBox).

First install VirtualBox.

```bash
$ sudo dpkg -i virtualbox-5.1_5.1.18-114002-Ubuntu-zesty_amd64.deb
$ sudo apt-get install -f
``` 

The installer should complain and mention running ``/sbin/vboxconfig``.

```bash
$ sudo /sbin/vboxconfig
```

Now clone the repository.

```bash
$ sudo -i
# mkdir /root
# git clone https://github.com/karlding/module-signing.git
# cd /root/module-signing
```

Now generate a Machine Owner Key pair using ``openssl`` which will be used to sign kernel modules.

```bash
# openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out MOK.der -nodes -days 36500 -subj "/CN=Karl Ding/"
# chmod 600 MOK.priv
```

Now import the public key. You'll need to choose a temporary password to verify the key later.
```bash
# mokutil --import /root/module-signing/MOK.der
```

Reboot.

The bootloader should ask for the password. Now "Enroll MOK", and continue the boot process.

Now sign any VirtualBox modules.

```bash
$ sudo -i
# cd /root/module-signing
# ./sign-vbox-modules
# modprobe vboxdrv
```

You'll have to run this every time you update your kernel.
