---
layout: post
title: DIY kit for etcd - Detour with Vagrantfile
---

# DIY kit for etcd - Detour with Vagrantfile !!

I thought it would be good to have a quick understanding of `Vagrantfile` before creating a etcd cluster.

In the previous posts we talked at length on the use of Vagrant as a tool to solve our problem of creating an 
etcd cluster. In this post we shall do a analysis of Vagrant's core component i.e. Vagrantfile.

Vagrantfile is meant to be specific to a project. *A project does not necessarily translate into a single VM.* 
The coolest thing about a Vagrantfile is it can be version controlled. The syntax of this file is Ruby. 
Don't worry, you need not start learning Ruby now. Think it as another properties file (*i.e.  a set of  key=value pairs*).

The important concept of a Vagrantfile is how it gets loaded by Vagrant.

<br/>

## Logic building

Please do not fear these code snippets. I would suggest to read these without any assumptions on any programming language.
The code snippets are here, as these lines offer the simplest way to teach the power of a Vagrantfile.

> Tip: Please be advised not to refer to Ruby's syntax even though Vagrantfile understands Ruby.
Think a Vagrantfile to be a scoped syntax w.r.t Ruby. Your life will be easier then.

Often a project requires multiple VMs to be provisioned. Below snippet shows us how that's possible:

```ruby
(1..3).each do |i|
  config.vm.define "node-#{i}" do |node|
    node.vm.provision "shell",
      inline: "echo hello from node #{i}"
  end
end
```

> Tip: Remember to override locale in SSH session. Failing to do so causes failures if the guest
does not support host's locale.

> Tip: Learn various VM related config settings available under the namespace `config.vm`

> Tip: Learn Windows guest related settings via the `config.winrm` namespace

<br />

## References

- https://www.vagrantup.com/docs/vagrantfile
