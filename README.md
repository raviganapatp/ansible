`Note : This ansible playbook supports installation of Kubernetes till version 1.23`
```
TO-DO
1. From k8s 1.24, docker support is removed from kubernetes i.e docjershim component is removed from the k8s source code
2. As a result, we need to install container runtime cri-dockerd separately to support docker, else install other CRIs like cri-o/containerd etc
3. write code for supporting point 1 and 2 
```

# ansible code to install Docker/Kubernetes
This ansible playbook does below tasks:
1. Disable Swap
2. Install Docker
3. Install specified version of Kubernetes
   
## Pre-requisites:
i. `ansible` and `ansible-playbook` should be installed

## Steps
1. Update `kubernetes_version` and `kubernetes_version_rhel_package` with required kubernetes version 
2. Update master/worker IPs in the `k8s-setup-new.yaml` file 
3. Run ansible-playbook \
   `ansible-playbook k8s-setup-new.yml -vv --ask-pass` \
   This will prompt for password. Enter `root` credentials of the VMs. Assuming the password for all VMs are same 


### Below is the example `k8s-setup-new.yaml` file
```
---  
#- name: create user with sudo access
#  hosts: 
#  - 10.106.179.71
#  - 10.106.179.72
#  - 10.106.179.73
#
#  remote_user: root
#  
#  vars: 
#    username: 
#      - { name: "kubeuser", state: present, sudo: "yes" }
#    groupname: 
#      - { name: "admin" , state: present }
#    # Pass "password" from command line as mentioned above 
#
#  roles:
#  - manage_user

- name: Kubernetes setup on Master
  hosts: 10.106.143.137
  remote_user: root

  vars:
    kubernetes_allow_pods_on_master: false
    kubernetes_pod_network:
      cni: 'calico' 
      cidr: '192.168.0.0/16'
    kubernetes_role: 'master'
    kubernetes_version: '1.23.4'
    kubernetes_version_rhel_package: '1.23.4'

  roles:
    - { role: ansible-role-swap, swap_file_state: absent }
    - ansible-role-docker
    - ansible-role-kubernetes

- name: Kubernetes setup on Workers
  hosts: 
  - 10.106.143.138
  - 10.106.143.139
  remote_user: root

  vars:
    kubernetes_role: "node"
    kubernetes_version: '1.23.4'
    kubernetes_version_rhel_package: '1.23.4'

  roles:
    - { role: ansible-role-swap, swap_file_state: absent }
    - ansible-role-docker
    - ansible-role-kubernetes
```
