CHANGES MADE TO MAKE THINGS WORK

A. ansible-role-kubernetes : 
   1. This seems to be tested on the 1.20 k8s version. By default changing the k8s version to `1.23.4` failed.
   Sol : Had to change in `defaults/main.yml`
   #kubernetes_version_kubeadm: 'stable-{{ kubernetes_version }}'  # stable-1.23.4 was not identified as version , hence failing
    kubernetes_version_kubeadm: 'v{{ kubernetes_version }}'        # v1.23.4 is vaid version, hence working
