# DPKG

Debian Software Package Management

```
$ sudo dpkg -i name_of_package.deb # 
$ sudo dpkg -r name_of_package # remove a package
$ dpkg -P googler_3.3.0-1_all.deb # remove everything including conf files
$ dpkg -l # list all packages
$ dpkg -c flashplugin-nonfree_3.2_i386.deb # view the content of the particular package
$ dpkg --unpack flashplugin-nonfree_3.2_i386.deb #  unpack the package.
$ dpkg --configure flashplugin-nonfree # can be later configured
$ dpkg -s git # check if package is installed
$ dpkg -L git # location of the installed package
$ dpkg --audit # check any issues
```

