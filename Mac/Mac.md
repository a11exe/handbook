# Mac handbook

* [Terminal](#terminal)
* [Brew](#brew)
* [Find file](#find-file)

## Terminal

`Ctrl + L` clear screen
`Ctrl + D` split screen
`Ctrl + Shift + D` remove split

## Brew
`brew install git`	Install a package
`brew uninstall git`	Uninstall a package
`brew upgrade git`	Upgrade package
`brew switch git 1.0.0`	Switch package version
`brew list --versions git`	List the installed versions of package
`brew services` List running services
`brew services stop postgresql@14` Stop service

## Find file
```
find / -name abc.dmg 2>/dev/null
```

## Add path
```
export PATH=/opt/homebrew/Cellar/postgresql@14/14.8_2/bin:$PATH
```