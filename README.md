# Build a Kubernetes cluster using k3s via Ansible

These playbooks are for test and development to enable a K3s cluster up and running on Proxmox, Vagrant, VMware or Hyper-v, tested on Proxmox and Hyper-v.

## K3s Ansible Playbooks

- [X] Set up your Ansible environment
- [X] Ensure you have Ansible installed on your control machine AKA ACS Server (the machine from which you will run the playbooks)
- [x] Highly Recomended [Ansible Collections](https://docs.ansible.com/ansible/latest/collections_guide/collections_installing.html)
- [X] Test deployment environment must have Ansible Core 2.14.17+

1. site.yml - main playbook.
2. update_ubuntu.yml - patches the VM's to the latest patch level (optional).
3. update_hosts.yml - updates the hosts with static IP's if no DNS available, edit the hosts file under files firectory (optional)
4. install_k3s.yml - install's k3s - master and ndoes.
5. install_helm - install helm charts (optional).

## System requirements

- [X] Master and nodes should have password-less SSH access (Optional)
- [x] Secure your Ansible connection: Ensure that Ansible can connect to your hosts. You might need to set up SSH keys and copy them to your hosts
- [X] Ubuntu 22.04 Server  x 1 Master (Control Plane) 2GB Ram 2 Cores, 2 x Nodes 2GB Ram 2 Cores

Processor architecture:

- [X] x64
- [X] arm64
- [X] armhf

## Usage

first, edit `inventory` to match the system information, for example:

```
[master]
k3s1 ansible_host=master_ip

[node]
k3s2
k3s3

[k3s_cluster:children]
master
node

[k3s_cluster:vars]
ansible_ssh_user=ubuntu
ansible_ssh_pass=
```
Start provisioning of the Kubernetes cluster using the following command:

```
Test the VM's respond to an Ansible ping
ansible '*' -m ping

ansible-playbook site.yml -v
ansible-playbook -i inventory site.yml -v

```

## To get access to your **Kubernetes** cluster just

```
ssh ubuntu@master_ip
```

## Check Cluster are up and running
The kubectl get svc command is used to list all the services in a Kubernetes cluster. This command provides a table of the most important information about the specified services, such as their names, types, cluster IPs, external IPs, ports, and more.

```
sudo kubectl get service 
sudo kubectl get svc 

sudo kubectl get all --all-namespaces
sudo kubectl get pods --all-namespaces -o wide
sudo kubectl get nodes
sudo kubectl get nodes -o wide
sudo kubectl get nodes --show-labels=true
kubectl config view --raw > ~/.kube/config # As root
```

## Check Helm is installed and running
[Helm Cheatsheet](https://helm.sh/docs/intro/cheatsheet/)

```
sudo helm --help
sudo helm repo list
```

## Install Helm manually
```
sudo snap install helm --classic
sudo helm repo add stable https://charts.helm.sh/stable
sudo helm repo update
sudo helm repo add "stable" "https://charts.helm.sh/stable" --force-update
sudo helm repo list
```

## Example install Jenkins with Helm
```
sudo helm repo add jenkins https://charts.jenkins.io
sudo helm repo update
sudo helm install myjenkins jenkins/jenkins
```

## Additional Notes
- Ensure you have proper permissions and SSH access to all nodes
- Adjust the playbook as needed for your specific environment (e.g., use different users, specify custom paths, etc.)
- Test the playbook on a non-production environment before applying it to your production servers
- This setup will install K3s on the specified servers and agents, forming a K3s cluster managed by Ansible

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License
[MIT](https://choosealicense.com/licenses/mit/)

## Author Information

[Author: Joey Allen](https://github.com/allen-joey)