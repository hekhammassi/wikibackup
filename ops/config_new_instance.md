---
title: Configuration d'une nouvelle instance
description: 
published: true
date: 2024-06-07T14:00:33.716Z
tags: 
editor: markdown
dateCreated: 2023-09-20T09:42:36.310Z
---

# Configuration d'une nouvelle instance


## bashrc

`nano /root/.bashrc`

``` 
export PS1='\[\033[31;1m\]\h\[\033[0m\]:\w\$ '
umask 022

export LS_OPTIONS='--color=auto'

alias ll='ls $LS_OPTIONS -l'
alias rm='rm -i'
alias cp='cp -v'
alias mv='mv -iv'
alias sl='ls'
alias nocom='egrep -v "^$|^#"'

export HISTFILESIZE=5000
export HISTSIZE=5000
export PATH=$PATH:~/bin
export VISUAL=vi
export EDITOR=vim
``` 

## History

`nano /etc/profile.d/users.sh`

```
#!/bin/sh -u

export HISTFILE=$HOME/.bash_hist-$(who am i | awk '{print $1}';exit)
``` 


## SSH

`/etc/ssh/sshd_config`

```
Port 36987
AuthorizedKeysFile      /etc/ssh/authorized_keys/%u
```

```
mkdir /etc/ssh/authorized_keys
```

```
nano /etc/ssh/authorized_keys/root 
```
```
ecdsa-sha2-nistp521 AAAAE2VjZHNhLXNoYTItbmlzdHA1MjEAAAAIbmlzdHA1MjEAAACFBAFkz2OeaTzNIJ+srbPFCIE1967HDpAkb8QSZC07W6o4xRQJPZiLO18x3f1daCy+RUWA2RRAT6MyRTAHtDF/kExUrwFvOPz+Vfz6AxhMpwSbxy6oWmSXBvrTwI3BL/JbnYpVj473h0S0upbtQSq+dydUllEoFTofXcJ5YoP+Gyr08LjM2A== mbi@malik-desktop
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC6HeLkfDKRStQWgouHdQzFqn3lvKZ+MoaoeHJgkfNhfr5s47YMIVwrIoFEZbX/QO7ACnP7pQSRnWQeMwluuG3WNdF1KByMYsYqLS0D3H2ywJGCAHLzzLtvlIDMsz4k3kDG/zpWo7cUYcV+1k1yeDuBSHuQJd1BS25xtNPfJs6maefmaJSoUKWM6XSb9ptdN/5YS/m9W+e0FLO1WKeurDAEJgytCzXv/wlzr7rMGlPbqQ9KQN4f2adZUh5sBLJyAODC+anMKrzfuWSsWZrDTyUrNWJ1eC0vyAJmu3jbJhG4lnEKYH2Mqp31TSMnAnkUgxSKD0WLjIuj8NtJS14bpVtkKGAooTETu31STHwEBu1xGscbbW1lCeGB4ys4/hnfQdR0eFG8tyH3XU5XFKs18IZ6JxvYQweDBtRs4hwr/O2k3ekP8NcZWrWntkNZanwYvPNTHU7VwZtLFAG8imMuk7Z2LTg4CwWGY23vsIjuyxsBFuso5fgfFZfSrMAhLcvmVK8= hek@hek-OptiPlex-3050
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC/I2Yk06pER6YfiNzM91Ii/9KfYQTbmUOsHN2TwLAa6XOv3+OD6o+2EvuEjTuc41sU3gmQvSgdR5/xdzU1CpS+EGpf4IfuSsJNAqMkzksNX+Ngmn/7xowr6JqcMKo2f8x1XfxcFzHuogt7pwZJCrU+boi9ZoPnUT47hwm0Z6iky1OjmapB6Fnu87XZJQ/rPs6/ABg62IL0/Zxac9EjUx16nSIcyeyrTIyI3IKIYBx74CdtWsd+3hjMg+zSLNinflLwuT2+FE6H3+KkHnkkyxIEjZ7YAd3oPlur4BtgU1uB7FK29/7Kn4QplLRfyujoxbw3RDbi1wJYS5SE8ncptOIbV9nxfPPu0W5JNHKEYh5eAbP5hoLFvfzg0m/8FZerr/Z++yXEGXqEdcLn7A3VNnS05D1giiSR0H9XB3718fiW9Nrmm5oegpG/3ryrQMtfMdD4ww9N1LZ5avj6OYR33fr83uoywZFlbuXhiXDySpf5hqs/qehJrhlTII+ekiCxr5k= gfh@ad-OptiPlex-3070
```

```
service ssh restart
```

## Test

> Ouvrir un nouveau terminal et tester la connexion. NE PAS FERMER L'ANCIEN TERMINAL TANT QUE LA CONNEXION NE FONCTIONNE PAS
{.is-warning}

`ssh root@IP_SERVER -p 36987`

Si la connexion ne fonctionne pas verifier votre configuration