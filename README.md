# Portable GPG SSH key encryptor
A small personal collection of shell scripts used for encrypting/decrypting and loading SSH keys into a workstation. Created with [sources](https://gist.github.com/stupakov/11227904) [all](https://github.com/DamnedFacts/ssh-fob) [over](http://tammersaleh.com/posts/building-an-encrypted-usb-drive-for-your-ssh-keys-in-os-x/) the internet. If you’re looking for a more out-of-the-box solution I would recommend something like [Yubikey](https://www.yubikeyshop.nl).

> The one key to rule them all

## Usage
I have only tested this with a USB / SD card never with high volume devices. When referring to 'Your drive', 'A drive', 'The drive' I am referring to a USB / SD card or [a SD card in a reader](https://www.kingston.com/us/flash/readers/fcr-mrg2).

- Make sure your drive is using the UDF format (See formatting).
- Clone this repository to the root directory of your drive.
- (Optional) Remove the README.md file from your drive.
- Go to or open a command line in your drives root directory.
- Run `sh init` to generate the correct directories, permissions and a gpg masterkey (See generating the masterkey!). 
- (Optional) Remove the init script from your drive.
- Open the `globals` file in the `script` directory and set your masterkey identifier under `KEY_OWNER_NAME`.
- Run `sh encrypt` to encrypt the ssh and sshfull directory.
- Add keys to your encrypted files with `sh add yourkey.key` or a whole directory using `sh add dirname`.
- Load a key by its name with `sh load keyname` or load all your keys with `sh load --all`. You can define the amount of time in hours with the second argument `sh load id_rsa 2` the following will load id_rsa into the ssh-agent for 2 hours.


Additional:
- Run `sh status` to check the status of the drive.
- Run `sh decrypt` to decrypt all files.
- Run `sh copy dirname` to copy the contents of your drive to another directory/drive.

Note: Protect your private keys with a passphrase otherwise they're susceptible to be compromised when loading into a ssh-agent.

## Commands
### `sh init`
Initializes your drive with the correct directories/permission. Create directories `ssh`, `sshfull` and `masterkey` and chowns the directories recursively to 700.

**WARNING:** Can cause unexpected results when run on a drive that is already initialized.

### `sh encrypt`
Encrypt the `ssh` and `sshfull` directories. Encrypts the ssh directory by making a tar.gz of it and encrypting the tar.

### `sh decrypt`
Decrypt the `ssh` and `sshfull` directories. Decrypts the directories and then untars it.

### `sh add <dirname/filename>`
Adds private key files to the encrypted `ssh` directory. This will prompt you for your masterkey passphrase.

**Directories:** If you set a directory, this command will decrypt and untar the `ssh` and `sshfull` directories and move the directory to `sshfull`. Any private keys found in the directory will be copied to `ssh` so they can be used with the `sh load` command.

**Files:** If you set a file this command will decrypt and untar the `ssh` directory. And add the file to the `ssh` directory, there is no check in this case wether or not the file is a private key, so make sure it is.

### `sh load <keyname> <hours>`
Removes all previous loaded keys and adds the specified key to the ssh-agent for N hours. By default the command will add your key for 1 hour, the hours argument is not required. This command decrypts and untars the `ssh` directory and will load a key matching your keyname. 

E.G: `sh load id_rsa 2` will load the id_rsa key for 2 hours.
E.G: `sh load common` will load the common key for 1 hour.

### `sh show <keyname>`
Prints the public key for they private/public key pair with the name `<keyname>`. Useful for adding the public key to services like Bitbucket, Github, etc...

### `sh endsession`
Forcefully clear the gpg-agent files from your `masterkey` directory and clear loaded ssh keys from the ssh-agent.

### `sh copy <directory>`
Clears the target directory and copies the contents of your drive to the target directory. Usefull for copies of your drive which you (should) keep [somewhere save and protected](http://www.homedepot.com/b/Tools-Safety-Security-Safes-Fire-Security-Safes/N-5yc1vZc29n).

**WARNING:** Backup the target directory! This will remove all the files in the target directory, use with caution!

## Formatting
I formatted my drive on MacOS (El Capitan) using [this blog post](http://www.sfcgeorge.co.uk/posts/2013/12/29/howto-format-udf-filesystem-osx-advantages). The format required for these scripts to run is `UDF`. For more information on what the format does and how it works please read the blog post. I'll quickly go over the steps again here in case the link is lost:

P.S: Replace all `{x}` and `{x}s{x}` with your drive number obtained by `diskutil list`

- Locate your drives drive number `diskutil list`
- Check block size of the drive `diskutil info /dev/disk{x} | grep "Block Size"`
- Unmount the drive to allow formatting `diskutil unmount /dev/disk{x}s{x}`
- Erase the drive `diskutil secureErase 0 /dev/disk{x}`
- Format the drive to UDF `sudo newfs_udf -b {block size} /dev/disk{x}`
- Mount the drive again `diskutil mount /dev/disk{x}`

## Generating the masterkey
The masterkey is used to encrypt all your private keys as a **second layer** of defence. Your private keys should always have a passphrase, even when loaded from an encrypted drive, into a trusted workstation.

The masterkey encryption protects your drive in case of loss. For example when you lose your key and some third party finds it. This third party now has encrypted private keys which are useless unless the passphrase for the masterkey is bruteforced. If the passphrase is bruteforced the third party needs to proceed to bruteforce the passphrase for your keys which should give you enough time to revoke access and create new keys.

If you choose to have no passphrase you might as well put all your keys in an unprotected directory on your drive.

So choose adequate protection for your masterkey.

## License
Portable GPG SSH key encryptor is released under a MIT license. Feel free to modify it any way you see fit! Of course pull-requests are preferred.