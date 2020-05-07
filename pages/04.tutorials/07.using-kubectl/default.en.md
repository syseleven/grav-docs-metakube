---
title: 'Using kubectl'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - kubernetes
        - cli
---

## Using a specified kubeconfig temporarily

See the [Official kubectl Install Instructions](https://kubernetes.io/docs/tasks/tools/install-kubectl/) for a tutorial on how to install kubectl on your system. Once you have it installed, [download the kubeconfig](../06.download-the-kubeconfig/default.en.md) and export it to your environment:

```bash
$ export KUBECONFIG=~/Downloads/kubeconfig-fhgbvx12xg
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.5", GitCommit:"...", GitTreeState:"clean", BuildDate:"...", GoVersion:"go1.9.7", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.3", GitCommit:"...", GitTreeState:"clean", BuildDate:"...", GoVersion:"go1.9.3", Compiler:"gc", Platform:"linux/amd64"}
```

## Using multiple kubeconfig files

If you regularly use different kubeconfig files, switching them manually can become tedious. To make this easier, we often use a combination of tools:

* A `KUBECONFIG` variable that specifies **all** kubeconfigs we use
* [kubectx](https://github.com/ahmetb/kubectx)

When configured, you can use `kubectx mycontext` to switch to a different context. By default, each kubeconfig file defines its own context - For more info on contexts, see the [documentation about contexts](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/#context).

To achieve this:

* [Install kubectx](https://github.com/ahmetb/kubectx#installation) for your operating system
* Create the directory `~/.kube/configs`
* Move all desired kubeconfigs to `~/.kube/configs`
* Expand the configuration of your shell:

```sh
# This is tested for bash and zsh
export KUBECONFIG="$(find ~/.kube/configs/ -type f -exec printf '%s:' '{}' +)"
```

* Now, once you open a new shell, your `KUBECONFIG` environment variable will contain all files in the `~/.kube/configs` directory
* Run `kubectx` to get an overview over all available contexts
* Run `kubectx context` to switch to the specific context

```bash
$ kubectx
admin@k8s-workshop1/workshop01
admin@k8s-workshop1/workshop02

$ kubectx admin@k8s-workshop1/workshop02
Switched to context "admin@k8s-workshop1/workshop02".
```
