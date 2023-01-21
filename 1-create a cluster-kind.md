# Create a new cluster


## Install kubectl 
### Update the apt package index and install packages needed to use the Kubernetes apt repository:
```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```
### Download the Google Cloud public signing key:
```bash
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```
### Add the Kubernetes apt repository:
```bash
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
### Update apt package index with the new repository and install kubectl:
```bash
sudo apt-get update
sudo apt-get install -y kubectl
```
### check all version
```bash
apt-cache policy kubectl
````


## kubectl cmpletion

```bash
apt-get install bash-completion
```
## Enable kubectl autocompletion
```
echo 'source <(kubectl completion bash)' >>~/.bashrc
kubectl completion bash >/etc/bash_completion.d/kubect
```
## If you have an alias for kubectl, you can extend shell completion to work with that alias:
```
echo 'alias k=kubectl' >>~/.bashrc
echo 'complete -F __start_kubectl k' >>~/.bashrc
```
## Good alias
```bash
alias kg='kubectl get'
alias klo='kubectl logs -f'
```



# Installation Kind
#### To install, download the binary for your platform from “Assets”, then rename it to kind (or perhaps kind.exe on Windows) and place this into your $PATH at your preferred binary installation directory.

```bash 
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-linux-amd64
chmod +x ./kind
mv ./kind /usr/local/bin/kind
```

## kind bash completion

```
sudo kind completion bash > /etc/bash_completion.d/kind
sudo kind completion zsh > /etc/bash_completion.d/kind
```

## Creating a Cluster
Creating a Kubernetes cluster is as simple as `kind create cluster.`
```bash
kind create cluster
kind create cluster --image kindest/node:latest
```

## Deleting a Cluster
If you created a cluster with kind create cluster then deleting is equally simple:
```bash
kind delete cluster
```
If the flag --name is not specified, kind will use the default cluster context name kind and delete that cluster.

## install docker on ubuntu
```
apt update -y
apt install apt-transport-https curl gnupg-agent ca-certificates software-properties-common -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
apt install docker-ce docker-ce-cli containerd.io -y
usermod -aG docker $USER
newgrp docker
docker version
```


refrence : 
https://github.com/AhmadRafiee
https://cloudcone.com/


