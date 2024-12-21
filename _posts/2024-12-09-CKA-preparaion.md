---
layout: post
title:  "Notes from sample exam"
tags: en certificate notes k8s
---

This is a post with all notes I took after taking the `killer.sh` sample exam.

<!--more-->

# Mistake Journal
## Question 8
This is an unexpected mistake.

I forgot to check static pods under `/etc/kubernetes/manifests/`, should have remembered static pod is a common form for kube services to run.

Something I didn't know and should learn is to use `find /usr/lib/systemd | grep <service>` to see if service is registered and controlled by `systemd`.

In this question, `kubelet` is running as a process directly and `ps aux` can find it, which I did correctly.

I saw the deploy `coredns` and used it as a part of the answer, which was correct.

## Question 9
I skipped this question because I wasn't sure how to temporarily stop the `kube-scheduler`.

I tried to use `systemd stop kube-scheduler` but it didn't work. Of course, because the scheduler was running as a static pod. In the answer, it's suggested to kill the scheduler by moving the `/etc/kubernetes/manifests/kube-scheduler.yaml` to somewhere else than the current folder.

And regarding the second part, I don't know how to manually schedule a pod either. When reviewing the question, I added the `spec.nodeName` section and then the pod could be scheduled.

## Question 12
This is an unexpected mistake.

It's a difficult question, especially when it asks to run one pod on each node using topologyKey. I thought I finished the whole question correctly, but still failed when running two containers in one pod.

After checking, only container2 runs. It was because I wrote `containers` twice and the second one overwrote the first.

## Question 14
I forgot what was Service CIDR, or more precisely I forgot what was CIDR.

Just needed to check the `kube-apiserver.yaml`

CIDR is the ip range.

## Question 15
I skipped this question, because I stuck in the beginning, didn't know how to show the latest events by order.

I tried `k events -A --sort-by=<>`, but I should have tried `k get events -A --sort-by=<>`

And I didn't know how to kill a containerd container. Here's the steps:
```
$ crictl ps | grep <pod>
$ crictl rm <container-id>
```

## Question 16
I did the first half and skipped the second half. But I did both wrong.

The first half was an unexpected mistake. Instead of giving the resource list, I put the command in given file.(But the command was correct)

Then the second half I didn't know how to list role count of a namespace. The answer is surprisingly easy:
`k -n <ns> get role --no-headers | wc -l`
and then run them one ns after another.

## Question 21
This is an unexpected mistake.

I used `kubectl expose`, but forgot to rename the svc as expected.
## Question 22
This is an unexpected mistake.

The time was tight at that time and I got panic, so I didn't read the question thoroghly. I wrote the wrong command in the file.

# Summary of actions and references
## Pre-setup
```shell
alias k=kubectl
export do="--dry-run=client -o yaml" # e.g. k create deploy nginx --image=nginx $do
export now="--force --grace-period 0" # e.g. k delete pod x $now

echo "set tabstop=2" >> ~/.vimrc
echo "set expandtab" >> ~/.vimrc
echo "set shiftwidth=2" >> ~/.vimrc
```
The first two lines are the most important ones.

BTW the cheat sheet in Kubernetes documentation now is called kubectl Quick Reference. Don't forget to have it open.

## Contexts
Context related commands can all be found in `k config -h`

One thing to notice is `k config get-contexts` can be used with flag `-o name` (You can get this info with `k config get-contexts`) It's interesting that many kubectl command can use the `-o` flag, but differently. The full list of `-o` usage can be found in quick reference (search "formatting output"!) and check the `-h` output for exactly which formats are allowed for a command.

Without using `kubectl`, the context information usually can also be found in `~/.kube/config`.

## Taints and tolerances
Search this directly in the Kubernetes documentation and you can find examples.

## StatefulSet
When seeing pods running like replicas, when multiple pods having the same prefix, run a `k describe pod` should show if the replicas are from a deployment, a daemonset, or a statefulset. 

Or just run `k get deploy,ds,sts | grep <prefix>` to see.

Or check the labels `k get pod --show-labels`, one of the labels should show what type it is. I always forget the `--show-labels` flag, so I'm writing it here to remind myself.

`k scale` can be used to scale all three types.

## Sorting
`k get <whatever-you-want-to-get> --sort-by=<index-that-you-use-to-sort>`

In the quick reference there are some examples, but if not sure, what I would do is to run a `k get <type> <name> -o yaml` and check which json item should be used to sort.

## PV & PVC
Find examples in doc: Tasks -> Configure Pods and Containers -> Configure a Pod to Use a PersistentVolume for Storage

## Monitoring

If given condition that `metrics-server` is installed, then `k top` can be used to display resource usage.

In Quick Ref there are some examples.

## Controlplane components
- controller manager
- kube-apiserver
- kube-scheduler
- etcd
- (optional) cloud-controller-manager

## Node components
- kubelet
- (optional) kube-proxy
- container runtime

## RBAC
Find examples in doc: Reference -> API Access Control -> Using RBAC Authorization

You can easily find it by searching `RBAC`.
(What I don't like about the K8s doc is that you can never summarize a pattern of how the docs are structured. Better to use keyword searching.)

To test if a role works we can use `k auth can-i`

## RBAC combinations
1. Role + RoleBinding (in single namespace)
2. ClusterRole + ClusterRoleBinding (cluster-wide)
3. ClusterRole + Rolebinding (available cluster-wide but applied in single namespace)
4. Role + ClusterRolebinding (Not possible)

## Create DaemonSet
A tip is to use `k create deployment $do` then replace the `Deployment` kind with `DaemonSet`.

## Pods running on specific Nodes
Doc: Concepts -> Scheduling, Preemption and Eviction -> Assigning Pods to Nodes
Search "Pods to Nodes" to find it.

## CNI plugin
Can be checked by `find /etc/cni/net.d/`

## Resource count
Remember the `wc -l` command

## Container info
On the node, run `crictl ps` can get all running containers (this command should be given in the question, I believe)

You can also search for "crictl" in the Kubernetes doc, to find more commands to use.

## Secret
When using `k create secret` with the `--from-literal` flag, the values will be automatically encrypted.

## Certification expiration
The command: `openssl x509 -noout -text -in <crt file>`
Searching `openssl` in the Kubernetes doc can find this command

`kubeadm` also has a `certs` command to find the expiration date.

## Network Policy
Directly search for "nework policy" in the Kubernetes doc, examples can be found there.

Notice: The structure should be clear. For example,
```
  egress:
    -                           # first rule
      to:                           # first condition "to"
      - podSelector:
          matchLabels:
            app: db1
      ports:                        # second condition "port"
      - protocol: TCP
        port: 1111
    -                           # second rule
      to:                           # first condition "to"
      - podSelector:
          matchLabels:
            app: db2
      ports:                        # second condition "port"
      - protocol: TCP
        port: 2222
```
means allow output traffic to either db1 on port 1111 or db2 on 2222
while
```
  egress:
    -                           # first rule
      to:                           # first condition "to"
      - podSelector:                    # first "to" possibility
          matchLabels:
            app: db1
      - podSelector:                    # second "to" possibility
          matchLabels:
            app: db2
      ports:                        # second condition "ports"
      - protocol: TCP                   # first "ports" possibility
        port: 1111
      - protocol: TCP                   # second "ports" possibility
        port: 2222
```
means allow output traffic to either db1 or db2, on either port 1111 or 2222.

## Etcd snapshot save and restore
**I should check again the lecture of ETCD.**

`ETCDCTL_API=3 etcdctl snapshot save <.db file path>`
this command needs certification flags, which can be found in `kube-apiserver` config file.

Then stop all controlplane components.

and restore:
`ETCDCTL_API=3 etcdctl snapshot restore <the saved .db file> --data-dir <a data dir will later be used be etcd> <the cert flags>`

Then edit the etcd config file with the correct data directory. And bring back all controlplane components.
