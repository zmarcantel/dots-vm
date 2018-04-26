dots vm
===========

This repo contains a `Vagrantfile` that provisions itself against
the [dots](https://github.com/zmarcantel/dots) repo.


customization
---------------

All provisioning is done with the vagrant `inline` provisioner.
To make customization easy, the `$install_steps` variable can be
added and subtracted to.

By default you get a base system with:

1. most common things installed (git, make, wget/curl, etc).
2. neovim
3. compilers
    * rust (nightly)
    * gcc + clang
    * golang
4. the dotfiles


Delete lines from `$install_deps` to remove components.

**TODO**: when adding/removing compilers, pay attention to
the `$install_dots` setup as the `YouCompleteMe` config cannot
build for compilers which are not installed.




running
-------------

To create a machine:

```
$ vagrant up
```




updating
------------

If you need to modify an existing machine, make the needed
`Vagrantfile` changes and then:

```
$ vagrant provision
```



usage
------------

You can use the VM in one of two ways:


#### as an environment

In this mode, you literally `ssh` into the box, and use it as a full
environment.

The `virtualbox` machine's name is defined as the name of the current
directory. If you know this value (and want to type it, and not in a
script or something):

```
$ vagrant ssh CURRENT_DIRECTORY_NAME
```

... or for a generic directory:

```
$ vagrant ssh $(basename `pwd`)
```



#### as a worker

If you simply want to dispatch commands into it:

```
$ vagrant ssh CURRENT_DIRECTORY_NAME -c "your command list here"
```

... or for our lazy, alias-happy, friends:


```
$ vagrant ssh $(basename `pwd`) -c "your command list here"
```
