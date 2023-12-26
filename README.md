Install rke 
	- Put rke binary in path 
Install virtualbox 
 	- Create virtualbox 
	- Install docker, add user to dockerusers 
	- Poweroff virtual machine, clone with new mac address and UUID's 
	- create 3-4 clones 

Create a cluster.yml file in $PATH
	(The cluster file is here in the directory)
	The cluster is what 'defines' which nodes we'll be using for what, and how we'll connect to them, in our case, we have the user we'll be using, and our local user has ssh access to the given users (as I run ssh-copy-id in advance)

    - address: 192.168.182.194
      user: jack
      role:
        - worker
	(there is 1 with workplace and etcd, and 2 with worker roles)
run rke up to start the cluster (copy config file to .kube/config) 

To check nodes are in operational state: 
kubectl get nodes 

For the rancher machine we use another one of the clones, and ran the docker-compose (the file is here in the directory)
A minute later the service was available through the browser at the address 10.0.0.2:8443 (with a warning the the ssl certificate was not trusted) 
Followed the UI instructions and imported my existing cluster to rancher with 2 simple commands 


Phase 2:  
 - Creating namespace dev and setting it to default 
kubectl create namespace dev
kubectl config set-context --current --namespace=dev

wrote a simple app - as seen in the simple-webapp directory 
Created a basic Dockerfile which uses a requirements.txt file (pip)
Built and pushed the image with: (after login)
docker build -t icreatemyimage/flask_app:2 . 
docker push icreatemyimage/flask_app:2 

Created manifest (some of them with the kubectl get deployment -o yaml)
checked the app works using port-forward (this is before ingress was working) 

Added simple livenessProbe httpGet test that will test the / flask endpoint to make sure it responds 200

Created an nginx-based ingress that will expose the app 

Added the yaml files to a helm chart 


Backed up the cluster using 
rke etcd snapshot-restore --config cluster.yml --name my-snapshot
Destroyed the cluster using 
rke remove 
restored the the cluster with 
rke etcd snapshot-restore --config cluster.yml --name my-snapshot
