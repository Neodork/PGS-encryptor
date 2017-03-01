# Portable GPG SSH key encryptor
A small personal collection of shell scripts used for encrypting/decrypting and loading SSH keys into a workstation. Created with [sources](https://gist.github.com/stupakov/11227904) [all](https://github.com/DamnedFacts/ssh-fob) [over](http://tammersaleh.com/posts/building-an-encrypted-usb-drive-for-your-ssh-keys-in-os-x/) the internet. If you’re looking for a more out-of-the-box solution I would recommend something like [Yubikey](https://www.yubikeyshop.nl).

## Usage
I have only tested this with a USB / SD card never with high volume devices. When referring to 'Your drive', 'A drive', 'The drive' I am referring to a USB / SD card or [a SD card in a reader](https://www.kingston.com/us/flash/readers/fcr-mrg2).

- Make sure your drive is using the UDF format (See formatting!)
- Clone this repository to the root directory of your drive.
- Go to or open a command line in your drives root directory.
- Run `sh init` to generate the correct folders, permissions and a gpg masterkey
- Fill the `ssh` folder with private keys (and optionally `full-ssh` with private, public and root_ca keys)
- Run `sh encrypt` to encrypt the ssh and sshfull directory
- (WIP - THIS DOESNT WORK YET!) Run `sh load` or `sh load 2` to load your keys for N amount of hours.
- decrypt everything using `sh decrypt` this will of course ask for your passphrase.
- (WIP - THIS DOESNT EXIST YET!) Run `sh copy <yourvolume>` to copy your PGS setup to another drive.

Optional:
- Run `sh status` to check the status of the drive

Note: Protect your private keys with a passphrase otherwise they're susceptible to be compromised when loading into a ssh-agent.

## Formatting
I formatted my drive on MacOS (El Capitan) using [this blog post](http://www.sfcgeorge.co.uk/posts/2013/12/29/howto-format-udf-filesystem-osx-advantages). The format required for these scripts to run is `UDF`. For more information on what the format does and how it works please read the blog post. I'll quickly go over the steps again here in case the link is lost:

P.S: Replace all `{x}` and `{x}s{x}` with your drive number obtained by `diskutil list`

- Locate your USB / SD card drive number `diskutil list`
- Check block size of the drive `diskutil info /dev/disk{x} | grep "Block Size"`
- Unmount the drive to allow formatting `diskutil unmount /dev/disk{x}s{x}`
- Erase the drive `diskutil secureErase 0 /dev/disk{x}`
- Format the drive to UDF `sudo newfs_udf -b {block size} /dev/disk{x}`
- Mount the drive again `diskutil mount /dev/disk{x}`

## License
Portable GPG SSH key encryptor is released under a MIT license. Feel free to modify it any way you see fit! Of course pull-requests are preferred.