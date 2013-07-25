If you want to mount USB in [Virtualbox][], you have to do this [solution][]. At
the time of this post, a typical Google search for this would go to a solution
at advocated using the `attachusb` command in `VboxManage` in the `Vagrantfile`
provider customization section. This will not work because the VM is off at the
time of bootup. It looks like the original author of this gist did not repost
his solution back to the thread.

The solution, in other words, is to use USB filters to automatically connect
devices to your VM. USB filters can be added while the machine is powered off.
They'll be applied upon boot or, in this case, `vagrant up`.

[Virtualbox]: http://virtualbox.org
[solution: https://gist.github.com/crazysim/c09924feb0830415a1d0#file-solution
