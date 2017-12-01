<center>
## rktlet and rkt as a Kubernetes CRI implementation
<img src="talk-rktlet/rkt-1.svg" width="100" />
</center>
​

<small>
Iago López Galeiras, Kinvolk
</small>



<center>
### About me
</center>
* Living in Berlin
* Kinvolk co-founder
* Worked a lot on rkt and rktlet
* Currently a maintainer of rkt and rktlet

```
* github: https://github.com/iaguis
* twitter: @iaguis
```
Note:
Kinvolk: for-hire Linux engineering team working on core cloud infrastructure components



<center>
<img src="talk-rktlet/rkt-1.svg" width="100" />
</center>
<center>
a modern, secure, composable container runtime
simple CLI tool<br />
golang + Linux<br />
self-contained<br />
init system/distro agnostic
</center>
Note:
* although we're init system agnostic we do unashamedly optimize for systemd



<center>
### simple CLI tool
</center>
```
bash/systemd/runit
 └── rkt (stage0)
      └── pod (stage1) nspawn|kvm|fly
           ├── app1 (stage2)
           └── app2 (stage2)

```
Note:
* Three different stages
* stage0: your typical CLI tool
* stage1: the actual container. Swappable.
* stage2: the app itself



<center>
### kubernetes
</center>
<section data-background-image="talk-rktlet/k8s.svg" data-background-size="contain">
</section>
Note:
* Kubernetes is basically an API to schedule containers
* You don't care about where your app runs
* Scales horizontally
* etcd distributed key-value store
 * single source of truth for the cluster




<center>
### kubelet + CRI
</center>
<small>
* CRI defines a `Runtime` interface
 * `RunPodSandbox()`
 * `PodSandboxStatus()`
 * `CreateContainer()`
 * `StartContainer()`
 * `StopPodSandbox()`
 * `...`
* Kubelet calls those methods via gRPC
* Shims implement it
 * cri-containerd
 * cri-o
 * **rktlet**

</small>
Note:
* gRPC: remote procedure call by google
 * kubelet will call method
 * rktlet will be on the other side to do the work



<center>
### rktlet
</center>
The rkt implementation of a Kubernetes Container Runtime
</center>
<section data-background-image="talk-rktlet/rktlet-cri.svg" data-background-size="contain">
</section>
Note:



<center>
### rkt: app experiment
</center>
<small>
* New rkt subcommand: app
 * rkt app sandbox
 * rkt app add
 * rkt app start
 * ...
* For now under `RKT_EXPERIMENT_APP`

</small>
Note:
* rkt has the pod concept but it was originally immutable
* These map pretty cleanly to systemd
 * add: create a new unit file and load it
 * start: start the unit file
 * etc.



<center>
### rkt: CRI logs
</center>
<small>
* rkt logging was journald
* CRI wants logs in custom text format
* Initial solution
 * journal2cri sidecar
  * Unreliable
  * Resource-consuming
* Proper solution
 * `RKT_EXPERIMENT_ATTACH`
 * iottymux
 * write directly into CRI format

</small>



<center>
### iottymux
</center>
</center>
<section data-background-image="talk-rktlet/iottymux.svg" data-background-size="contain">
</section>



<center>
### What comes next?
</center>
<small>
* Update rkt's CNI version to 0.6.0
* Stabilize rkt experiments and remove `RKT_EXPERIMENT_*` env variables
* Experiment with using `runc` to set up stage2 runtime
* Pass more k8s e2e conformance tests
  * Current: 130 Passed | 15 Failed | 145 Total
* `kubectl attach`
* Performance

</small>
Note:
* K8s updated CNI to 0.6.0 recently
* Code in iottymux is not *ideal*
* Problem: using systemd unit files, we're behind runc



<center>
# Thank You!
</center>
<center>
## Questions?
</center>
Note:
* The end!
