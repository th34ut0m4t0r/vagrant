---
layout: documentation
title: Changes - 0.1.x to 0.2.x
---
# Changes from Version 0.1.x to 0.2.x

`0.1.x` was an experimental version for Vagrant. It was the first public
release of Vagrant and it allowed us to get feedback from users of where
they'd like to see it head. It also allowed us to focus on stability and
cross-platform support, since initially it was only known to run on Mac OS X 10.6,
but Vagrant `0.1.4` now runs on multiple platforms.

This page will outline the major changes from `0.1.x` to `0.2.x`, starting
with the backwards incompatible changes.

## Backwards Incompatible Changes

**Vagrant no longer supports password-based SSH.** Initially, Vagrant _only_
allowed for password based SSH, but Vagrant now _only_ supports key-based
authentication. Most users will need to update their base box to support this,
which is very simple. Read more in the [key-based SSH authentication](#key-based-ssh) section.

**Provisioning Configuration Changed.** If you were using chef solo provisioning
in `0.1.x`, it now must be enabled with `config.vm.provisioners = :chef_solo`.
This is because `config.chef.enabled` no longer exists. Read more about this in the
[multiple provisioner support](#multiple-provisioners) section.

<a name="key-based-ssh"> </a>
## Public/Private Key SSH. No more password SSH support.

While addressing many of the issues users were having getting Vagrant working,
we continually ran into the problem of users not having the `expect` dependency,
which was used for us to SSH via passwords (for `vagrant ssh`). Additionally,
this sort of dependency does not exist natively for Windows, which we would like
to support as well.

We therefore decided to drop support for password SSH completely and only support
SSH via keypairs. This has the benefit of being much simpler for us to program
into Vagrant, and is also much simpler for base box creators to easily setup
their boxes for Vagrant by using the Vagrant insecure keys.

Vagrant now includes two [insecure keys](http://github.com/mitchellh/vagrant/tree/master/keys/) which can be used
to authenticate to public boxes. Public boxes should allow SSH access to the `vagrant`
user via the public insecure key, and Vagrant by default will use the private
insecure key to attempt to access a virtual machine.

For users who require more security, they are welcome to use their own keypair
with their box. Vagrant has the `config.ssh.private_key_path` configuration for
just that reason. By setting that to a different private key, Vagrant will
attempt to use that to SSH, instead.

<a name="multiple-provisioners"> </a>
## Multiple Provisioners Support

In Vagrant `0.1.x`, if you wanted provisioning support, you were forced to use
chef solo. In `0.2.x`, Vagrant allows you to use anything you desire, since new provisioners
can be created by subclassing `Vagrant::Provisioners::Base`.

Provisioners are now specified via the `config.vm.provisioners` configuration.
There are symbol shortcuts for the built-in provisioners, but by setting this to
a class which subclasses `Vagrant::Provisioners::Base`, Vagrant will use this instead.

This opens up the possibility for tools like Puppet in addition to Chef.

This means that if you were using chef solo before, you must now enable it like so:

{% highlight ruby %}
Vagrant::Config.run do |config|
  config.vm.provisioner = :chef_solo
end
{% endhighlight %}

For more information on the new provisioners, read the detailed [provisioners](/docs/provisioners.html) section.

<a name="enhanced-chef-support"> </a>
## Enhanced Chef Support, including Chef Server Support
