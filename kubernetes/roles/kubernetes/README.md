# Ansible Role: Kubernetes

An Ansible Role that installs [Kubernetes](https://kubernetes.io) on
Centos Linux.

## Requirements

Requires Docker.

## Role Variables

Available variables are listed below, along with default values (see
`defaults/main.yml`):

    kubernetes_packages:
      - name: kubelet
        state: present
      - name: kubectl
        state: present
      - name: kubeadm
        state: present
      - name: kubernetes-cni
        state: present

Kubernetes packages to be installed on the server. You can either
provide a list of package names, or set `name` and `state` to have
more control over whether the package is `present`, `absent`,
`latest`, etc.

    kubernetes_version: '1.15' kubernetes_version_rhel_package:
    '1.15.3'

The minor version of Kubernetes to install. The plain
`kubernetes_version` is used to pin an apt package version on Debian,
and as the Kubernetes version passed into the `kubeadm init` command
(see `kubernetes_version_kubeadm`). The
`kubernetes_version_rhel_package` variable must be a specific
Kubernetes release, and is used to pin the version on Red Hat / CentOS
servers.

    kubernetes_role: master

Whether the particular server will serve as a Kubernetes `master`
(default) or `node`. The master will have `kubeadm init` run on it to
intialize the entire K8s control plane, while `node`s will have
`kubeadm join` run on them to join them to the `master`.

    kubernetes_kubelet_extra_args: ""
    kubernetes_kubelet_extra_args_config_file: /etc/default/kubelet

Extra args to pass to `kubelet` during startup. E.g. to allow
`kubelet` to start up even if there is swap is enabled on your server,
set this to: `"--fail-swap-on=false"`. Or to specify the node-ip
advertised by `kubelet`, set this to `"--node-ip={{ ansible_host }}"`.

    kubernetes_kubeadm_init_extra_opts: ""

Extra args to pass to `kubeadm init` during K8s control plane
initialization. E.g. to specify extra Subject Alternative Names for
API server certificate, set this to: `"--apiserver-cert-extra-sans
my-custom.host"`

    kubernetes_allow_pods_on_master: true

Whether to remove the taint that denies pods from being deployed to
the Kubernetes master. If you have a single-node cluster, this should
definitely be `True`. Otherwise, set to `False` if you want a
dedicated Kubernetes master which doesn't run any other pods.

    kubernetes_enable_web_ui: false kubernetes_web_ui_manifest_file:
    https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml

Whether to enable the Kubernetes web dashboard UI (only accessible on
the master itself, or proxied), and the file containing the web
dashboard UI manifest.

    kubernetes_pod_network_cidr: '10.244.0.0/16'
    kubernetes_apiserver_advertise_address: ''
    kubernetes_version_kubeadm: 'stable-{{ kubernetes_version }}'
    kubernetes_ignore_preflight_errors: 'all'

Options passed to `kubeadm init` when initializing the Kubernetes
master. The `kubernetes_apiserver_advertise_address` defaults to
`ansible_default_ipv4.address` if it's left empty.


    kubernetes_flannel_manifest_file_rbac:
    https://raw.githubusercontent.com/coreos/flannel/master/Documentation/k8s-manifests/kube-flannel-rbac.yml
    kubernetes_flannel_manifest_file:
    https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

Flannel manifest files to apply to the Kubernetes cluster to enable
networking. You can copy your own files to your server and apply them
instead, if you need to customize the Flannel networking
configuration.

## Dependencies

None.
