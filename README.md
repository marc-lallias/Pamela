# Pamela
Security project during my studies at Epitech.
The purpose of this project is to create a pam module to open some cipher directories/partitions when a user open his session.

## Add a new user {user}
``
sudo adduser {user}
``

## Download cryptsetup
``
sudo apt-get install cryptsetup
``
## Add a new cipher of {number} MiB directory for {user}
### Creation of a file that will be converted into cipher container
``
dd if=/dev/urandom bs=1M count={number} of=/home/{user}/cipher{user}
``

#### Be careful below, the password that you give to the container MUST be the same as your user session password, indeed, PAM just pass your session password to the container at the opening of your session !
#### P.S: You must also write YES in capital letters in order to accept the creation of the container.

### Creation of a cipher container
``
sudo cryptsetup luksFormat /home/{user}/cipher{user}
``

### OPTIONAL Add a key to your container so that another {user} can open it (to make a shared container for example) :
``
sudo cryptsetup luksAddKey /home/{user}/cipher{user}
``

### Opening of the cipher container
``
sudo cryptsetup luksOpen /home/{user}/cipher{user} cipher{user}
``

### Format the container on Ext4
``
sudo mkfs.ext4 /dev/mapper/cipher{user}
``

### Create the mount point
``
mkdir /home/{user}/cipher
``

## Modify the /etc/fstab file
### Add this line to your /etc/fstab file :
``
/dev/mapper/cipher{user}  /home/{user}/cipher ext4  defaults,noauto 0 0
``

## Download your dev environment :
### OPTIONAL Emacs
``
sudo apt-get install emacs
``

### libpam
``
sudo apt-get install libpam0g-dev
``

### make
``
sudo apt-get install make
``

### gcc
``
sudo apt-get install gcc
``

## NOW LET'S CODE TO MAKE A PAM MODULE !

### Build the pam module
You need sudo to be installed and your user added to the /etc/sudoers file
```
./build.sh
```

### Modify the /etc/pam.d/common-* files
You need to add this line at the end of the file /etc/pam.d/common-auth :
```
auth  optional  pam_module.so
```
This at the end of /etc/pam.d/common-session :
```
session optional  pam_module.so
```
This at the end of /etc/pam.d/common-account :
```
account optional  pam_module.so
```
And this at the end of /etc/pam.d/password :
```
password  optional  pam_module.so
```
