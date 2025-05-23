# Simplify Kubernetes Cluster Setup with K3s and Ansible Automation

Ansible Playbooks for Testing and Development of K3s Clusters on Proxmox, Vagrant, VMware, and Hyper-V (Tested on Proxmox and Hyper-V)

## K3s Ansible Playbooks

- [X] Set up your Ansible environment
- [X] Ensure you have Ansible installed on your control machine AKA (ACS Server) (the machine from which you will run the playbooks)
- [x] Highly Recomended [Ansible Collections](https://docs.ansible.com/ansible/latest/collections_guide/collections_installing.html)
- [X] Test deployment environment must have Ansible Core 2.14.17+

1. site.yml - main playbook
2. Ansible hosts - updates the hosts file with static ip and host names for the cluster (optional)
3. Ansible patching - updates the Ubuntu VM's latest updates
4. Ansible K3s - install's K3s - master and nodes

## System requirements

- [X] Master and nodes should have password-less SSH access (Optional)
- [x] Secure your Ansible connection: Ensure that Ansible can connect to your hosts. You might need to set up SSH keys and copy them to your hosts
- [X] Ubuntu 24.10+ Server  x 1 Master (Control Plane) 2GB Ram 2 Cores, 2 x Nodes 2GB Ram 2 Cores

Processor architecture:

- [X] x64
- [X] arm64
- [X] armhf

## Usage

first, edit `inventory` to match the system information, for example:

```
[master]
K3s1 ansible_host=master_ip

[node]
K3s2
K3s3

[K3s_cluster:children]
master
node

[K3s_cluster:vars]
ansible_ssh_user=
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
kubectl get service 
kubectl get svc 

kubectl get all --all-namespaces
kubectl get pods --all-namespaces
kubectl get pods --all-namespaces -o wide
kubectl describe pod <pod-name> -n <namespace>
kubectl get nodes
kubectl get nodes -o wide
kubectl get nodes --show-labels=true
kubectl config view --raw > ~/.kube/config # as root
```

## Install HELM Ubuntu
```
snap install helm --classic
helm repo add stable https://charts.helm.sh/stable
helm repo update
helm repo add "stable" "https://charts.helm.sh/stable" --force-update
helm repo list
```

## Check HELM is installed and running
[Helm Cheatsheet](https://helm.sh/docs/intro/cheatsheet/)

```
helm --help
helm repo list
```

## Install ArgoCD
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
After installation check all pods are created are running
```
kubectl get pods -n argocd
```
Access the ArgoCD UI by either by port-forwarding or exposing service as Nodeport. NodePort used here to expose.
```
kubectl expose service argocd-server --type=NodePort --name=argocd-server-nodeport -n argocd --port=80 --target-port=8080
```
Get the node port by checking the exposed service, argocd-server-nodeport.
```
kubectl get svc -n argocd
```
Access the ArgoCD web interface at https://IP:Nodeport

Retrieve the ArgoCD initial admin password.
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
Log in to ArgoCD using the username admin and the password. Change the password in the UI click on user Info (UPDATE PASSWORD)

## Example install Jenkins with Helm
```
helm repo add jenkins https://charts.jenkins.io
helm repo update
helm install myjenkins jenkins/jenkins
```

## Additional Notes
- Ensure you have proper permissions and SSH access to all nodes
- Adjust the role as needed for your specific environment (e.g., use different users, specify custom paths, etc.)
- Test the role on a non-production environment before applying it to your production servers
- This setup will install K3s on the specified servers and agents, forming a K3s cluster managed by Ansible
- Added a Jenkins file (You might need to run see below from your Ansible/Jenkins server)
```
runuser -u jenkins ansible-galaxy collection install kubernetes.core
```

# Cluster Access
## Accessing the Cluster from Outside with kubectl
[Docs K3S](https://docs.K3s.io/cluster-access)
Copy /etc/rancher/k3s/k3s.yaml on your machine located outside the cluster as ~/.kube/config. Then replace the value of the server field with the IP or name of your K3s server. kubectl can now manage your K3s cluster.

```
kubectl config view
```

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License
[MIT](https://choosealicense.com/licenses/mit/)

## Author Information

[Author: Joey A](https://linkedin.com//in/allen-joey)