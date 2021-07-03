Deployment of a 3 nodes(1 master and 2 workers) kubernetes cluster with vagrant and ansible.

Vagrant is used with libvirt provider and nodes are provisioned with ansible.


Dependencies:

Dependencies are installed with "dependency-installation.sh" script.
Dependecies are libvirt, vagrant and ansible.


Ansible provision:
 Total 5 roles are created to meet the goal.
 
 Roles description: {rolename(master/worker)}
  kube-prepare(master & worker): kubernetes dependant binaries are installed
  kubectl(master): installing kubectl for master only.
  kubeadm-config(master): configure the env with a kuneadm config file, generating kubeconfig
   and generating join token.
  join-node(worker): joinning worker node to the cluster
  metricserver-and-dashboard: installing metric server and dashboard and exposing nodeport  