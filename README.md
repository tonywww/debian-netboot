# Debian Network Reinstall Script

This script is written to reinstall a VPS/virtual machine to Debian 10 Buster.

(适用于甲骨文amd/arm系列VPS)

Project short url: https://git.io/debi


<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Introduction</summary>
  <ol>
    <li>
      <a href="#original-os">Original OS</a>
    </li>
    <li>
      <a href="#usage">Usage</a>
    </li>
    <li>
      <a href="#available-options">Available Options</a>
    </li>
    <li>
      <a href="#how-it-works">How It Works</a>
    </li>
    <li><a href="#license">License</a></li>
  </ol>
</details>


## Original OS

 * Debian 8 or later
 * Ubuntu 14.04 or later
 * CentOS 7 or later

## Usage

### Chinese Version:

以具有**sudo**特权的一般用户运行以下脚本:

    sudo bash <(wget -qO- https://git.io/debi.sh) --bbr --user root --password mypass)

或者以**root**用户直接运行以下脚本:

    bash <(wget -qO- https://git.io/debi.sh) --bbr --user root --password mypass)

 * 开启`TCP BBR`.
 * 如果不加`--user root`，默认管理员用户`debian`将创建(带有`sudo`权限).
 * 如果不加`--password <string>`会提示输入管理员密码.
 * 以上脚本默认管理员密码为`mypass`，可更改为你设定的密码.

如果脚本运行后没有报错，重启VPS进行自动安装. (需等待5-10分钟, 安装完毕后原登录key无效，仅使用密码登录.)

    reboot

参考: [甲骨文云服务器自动获取 IPv6](https://github.com/bohanyang/debi/wiki/%E7%94%B2%E9%AA%A8%E6%96%87%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%87%AA%E5%8A%A8%E8%8E%B7%E5%8F%96-IPv6)


### English Version:

Run the script from user with **sudo** privilegeunder: 

    sudo bash <(wget -qO- https://git.io/debi.sh) --bbr --user root --password mypass)

Or run the script under **root** : 

    bash <(wget -qO- https://git.io/debi.sh) --bbr --user root --password mypass)

 * Enable TCP BBR.
 * If you don't use `--user root`, an admin user `debian` with sudo privilege will be created during the installation.
 * If You don't use `--password <string>`, the password will be prompted.
 * Default root password is `mypass`. You may change to your own password.

If everything looks good, reboot the machine:

    reboot

Otherwise, you can run this command to revert all changes made by the script:

    rm -rf debi.sh /etc/default/grub.d/zz-debi.cfg /boot/debian-* && { update-grub || grub2-mkconfig -o /boot/grub2/grub.cfg; }

## Available Options

 * `--bbr` Enable TCP BBR congestion control
 * `--username, --user debian` New user with `sudo` privilege or `root`
 * `--password <string>` Password of the new user. **You'll be prompted if you choose to not specify it here**
 * `--network-console` Enable the network console of the installer. `ssh installer@ip` to connect
 * `--firmware` Load additional [non-free firmwares](https://wiki.debian.org/Firmware#Firmware_during_the_installation)
 * `--grub-timeout 5` How many seconds the GRUB menu shows before entering the installer
 *
 * `--ip <string>` Disable the auto network config (DHCP) and configure a static IP address, e.g. `10.0.0.2`, `1.2.3.4/24`, `2001:2345:6789:abcd::ef/48`
 * `--netmask <string>` e.g. `255.255.255.0`, `ffff:ffff:ffff:ffff::`
 * `--gateway <string>` e.g. `10.0.0.1`
 * `--dns '8.8.8.8 8.8.4.4'` (Default IPv6 DNS: `2001:4860:4860::8888 2001:4860:4860::8844`)
 * `--hostname <string>` FQDN hostname (includes the domain name), e.g. `server1.example.com`
 * `--suite buster`
 * `--mirror-protocol http` or `https` or `ftp`
 * `--https` alias to `--mirror-protocol https`
 * `--mirror-host deb.debian.org`
 * `--mirror-directory /debian`
 * `--security-repository http://security.debian.org/debian-security` Magic value: `'mirror' = <mirror-protocol>://<mirror-host>/<mirror-directory>/../debian-security`
 * `--no-account-setup, --no-user` **(Manual installation)** Proceed account setup manually in VNC or remote console.
 * `--authorized-keys-url <string>` URL to your authorized keys for SSH authentication. e.g. `https://github.com/torvalds.keys`
 * `--sudo-with-password` Require password when the user invokes `sudo` command
 * `--timezone UTC` https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List
 * `--ntp 0.debian.pool.ntp.org`
 * `--no-disk-partitioning, --no-part` **(Manual installation)** Proceed disk partitioning manually in VNC or remote console
 * `--disk <string>` Manually select a disk for installation. **Please remember to specify this when more than one disk is available!** e.g. `/dev/sda`
 * `--no-force-gpt` By default, GPT rather than MBR partition table will be created. This option disables it.
 * `--bios` Don't create *EFI system partition*. If GPT is being used, create a *BIOS boot partition* (`bios_grub` partition). Default if `/sys/firmware/efi` is absent. [See](https://askubuntu.com/a/501360)
 * `--efi` Create an *EFI system partition*. Default if `/sys/firmware/efi` exists
 * `--filesystem ext4`
 * `--kernel <string>` Choose an package for the kernel image
 * `--cloud-kernel` Choose `linux-image-cloud-amd64` as the kernel image
 * `--no-install-recommends`
 * `--install 'ca-certificates libpam-systemd'`
 * `--safe-upgrade` **(Default)** `apt upgrade --with-new-pkgs`. [See](https://salsa.debian.org/installer-team/pkgsel/-/blob/master/debian/postinst)
 * `--full-upgrade` `apt dist-upgrade`
 * `--no-upgrade` 
 * `--ethx` Disable *Consistent Network Device Naming* to get interface names like *ethX* back
 * `--hold` Don't reboot or power off after installation
 * `--power-off` Power off after installation rather than reboot
 * `--architecture <string>` e.g. `amd64`, `i386`, `arm64`, `armhf`, etc.
 * `--boot-directory <string>`
 * `--no-force-efi-extra-removable` [See](https://wiki.debian.org/UEFI#Force_grub-efi_installation_to_the_removable_media_path)
 * `--dry-run` Print generated preseed and GRUB entry without downloading the installer and actually saving them

### Presets

### `--cdn`

 * `--mirror-protocol https`
 * `--mirror-host deb.debian.org`
 * `--security-repository mirror`

### `--aws`

 * `--mirror-protocol https`
 * `--mirror-host cdn-aws.deb.debian.org`
 * `--security-repository mirror`

### `--china`

 * `--dns '223.5.5.5 223.6.6.6'`
 * `--mirror-protocol https`
 * `--mirror-host mirrors.aliyun.com`
 * `--security-repository mirror`
 * `--ntp ntp.aliyun.com`

## How It Works

1. Generate a preseed file to automate installation
2. Download the 'Debian-Installer' to the `/boot` directory
3. Append a menu entry of the installer to the GRUB2 configuration file

### Virtualization Platform

 * SolusVM/OpenStack/DigitalOcean/Vultr/Linode/Proxmox/QEMU KVM (BIOS boot)
 * Oracle Cloud Infrastructure (UEFI boot)
 * Google Cloud Compute Engine (**Must manually configure the VPC internal IP and the gateway.** UEFI boot with Secure Boot support)
 * AWS EC2 & Lightsail (BIOS boot)
 * Hyper-V & Azure (Generation 1 BIOS boot & Generation 2 UEFI boot)

## License
[BSD 2-Clause](LICENSE) © tonywww

Credit: bohanyang
