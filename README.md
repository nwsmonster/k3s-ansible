# Build a Kubernetes cluster using k3s via Ansible

## K3s Ansible Playbook

Ansible playbook for bootstrapping my personal cluster. See [k3s-io/k3s-ansible](https://github.com/k3s-io/k3s-ansible) to start your own.

## System requirements

Deployment environment must have Ansible 2.4.0+.  

Master and nodes must have passwordless SSH access.

```bash
pip install yamllint ansible-lint ansible
```

## Usage

Edit `inventory/prod/hosts.ini` to match the system information gathered above. For example:

```bash
[master]
192.168.70.100

[node]
# empty for single node cluster

[k3s_cluster:children]
master
node
```

If needed, edit `inventory/prod/group_vars/all.yml` to match your environment.

Start provisioning the cluster using the following command:

```bash
# add -K if the become user requires sudo access
ansible-playbook site.yml -i inventory/prod/hosts.ini
```

### Fix permissions

I was unable to use `kubectl` or `k3s kubectl` on the master node without doing this. Why?

> TODO: Do this with Ansible

On the master node:  

```bash
export KUBECONFIG=~/.kube/config
mkdir -p ~/.kube 2> /dev/null
if [ -f $KUBECONFIG ]; then mv $KUBECONFIG ${KUBECONFIG}.bak; fi;
sudo k3s kubectl config view --raw > "$KUBECONFIG"
chmod 600 "$KUBECONFIG"
```
