# cleanunusedfilesinarchlinux

## Contents
- Clean pkg cache
- Remove unused packages (orphans)
- Clean cache in /home
- remove old config files
- Find and Remove
   - duplicates
   - empty files
   - empty directories
   - broken symlinks
- Find Large files

## 1 Clean pkg cache
List packages
```bash
ls /var/cache/pacman/pkg/ | less 
```
Remove all pkg except those installed
```
sudo pacman -Sc 
```
Remove all files
```
sudo pacman -Scc
```
Download manually from archive.

### Automatically remove
```
sudo pacman -S pacman-contrib
```
Remove
```
paccache -r
```
Systemd timer
create file in `/etc/systemd/system/paccache.timer` with the following contents
```
[Unit]
Description=Clean-up old pacman pkg cache

[Timer]
OnCalendar=monthly
Persistent=true

[Install]
WantedBy=multi-user.target
```
Enable by `sudo systemctl start paccache.timer`

Pacman post-transaction hook


## 2 remove unused packages
List unused
```
sudo pacman -Qtdq
```
What -Qtdq will do according to the man page is (-Q stands for querying the package database):

       -d, --deps
           Restrict or filter output to packages installed as dependencies. This option can be combined with -t for listing real orphans
           - packages that were installed as dependencies but are no longer required by any installed package.

       -t, --unrequired
           Restrict or filter output to print only packages neither required nor optionally required by any currently installed package.
           Specify this option twice to include packages which are optionally, but not directly, required by another package.

       -q, --quiet
           Show less information for certain query operations. This is useful when pacman’s output is processed in a script. Search will
           only show package names and not version, group, and description information; owns will only show package names instead of
           "file is owned by pkg" messages; group will only show package names and omit group names; list will only show files and omit
           package names; check will only show pairs of package names and missing files; a bare query will only show package names rather
           than names and versions.
Which means that you have not directly installed this package, but it was installed as a dependency which was later removed. So if you are sure you want to keep this package (since you use it), you can just reinstall the package: sudo pacman -S linux-rt-manjaro and it should not be shown in the list of packages which get removed.

Not sure if Manjaro in general handles things differently, such that any of the above information is not valid in that case. It should be valid for Arch itself.



Remove unused
```
sudo pacman -R $(pacman -Qtdq)
```

## 3 Clean home cache
cache is located in ~/.cache

## 4 Config Files
stored in ~/.config/


## 5   Another thing I find now useful is to remove the cache of all uninstalled packages using:

$ paccache -ruk0

r: Remove packages
u: specifies uninstalled packages,
k0: "keep" 0 versions



## 6 Find and remove
install rmlint package `sudo pacman -S rm lint`.


use sudo pacman -Rs $(pacman -Qtdq)
instead of sudo pacman -R $(pacman -Qtdq) otherwise you would have to run it multiple times in a row


