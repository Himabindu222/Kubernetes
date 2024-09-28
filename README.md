# Kubernetes
Step-1. Installing Kubernetes:

Run below commands on Master and Worker node

sudo apt update

sudo apt install docker.io -y

sudo chmod 666 /var/run/docker.sock

sudo apt-get install -y apt-transport-https ca-certificates curl gnupg

sudo mkdir -p -m 755 /etc/apt/keyrings

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update

sudo apt install -y kubeadm=1.30.0-1.1 kubelet=1.30.0-1.1 kubectl=1.30.0-1.1

Run the below command in master node, join command will be generated to join worker node to master node.
sudo kubeadm init --pod-network-cidr=10.244.0.0/16   (run the join command in worker node)


mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml   (calico for networking)

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.49.0/deploy/static/provider/baremetal/deploy.yaml

Step-2. Create a file to create user ServiceAccount, clusterRole, clusterRoleBinding
vi admin.yaml  (copy the code from RBAC)

kubectl apply -f admin.yaml

Step-3. cat ~/.kube/config   (This generates a token )


-> For Admin Service Account
kubectl -n default create token admin

-> For General Service Account
kubectl -n default create token general

-> For Others Service Account
kubectl -n default create token others

Step-4. Create Kubeconfig Files

Use the tokens generated in the previous step to create kubeconfig files for each ServiceAccount.

-> In the user vm create a file kubeconfig-admin.yaml  (code is present in RBAC)

-> export KUBECONFIG=/path/to/admin-kubeconfig.yaml

export KUBECONFIG=/home/ubuntu/kubeconfig-admin.yaml   (now Linux understands that it need to use this file for authentication)
