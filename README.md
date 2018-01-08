# No API token found for service account "default"

[root@kubernetes-master pods]# kubectl create -f mysql.yaml 

Error from server (ServerTimeout): error when creating "mysql.yaml": No API token found for service account "default", retry after the token is automatically created and added to the service account

# Solve the certification problem:

1. Remove the certification

Create pod:

# kubectl create -f mysql.yaml
At this point the following error:

Error from server (ServerTimeout): error when creating "mysql.yaml": No API token found for service account "default", retry after the token is automatically created and added to the service account

The solution is to edit /etc/kubernetes/apiserver to remove SecurityContextDeny, ServiceAccount in KUBE_ADMISSION_CONTROL and restart the kube- apiserver.service service:

# vim /etc/kubernets/apiserver

KUBE_ADMISSION_CONTROL="--admission_control=NamespaceLifecycle,NamespaceExists,LimitRanger,ResourceQuota"

# systemctl restart kube-apiserver.service

After re-create pod:

# kubectl create -f mysql.yaml
pod "mysql" created


2. Create a certificate

Source:  
http://stackoverflow.com/questions/34464779/pod-mysql-is-forbidden-no-api-token-found-for-service-account-default-default

To get your setup working, you can do the same thing local-up-cluster.sh is doing:

Generate a signing key: 
# openssl genrsa -out /tmp/serviceaccount.key 2048

# Update /etc/kubernetes/apiserver: 
KUBE_API_ARGS=”–service_account_key_file=/tmp/serviceaccount.key”

# Update /etc/kubernetes/controller-manager: 
KUBE_CONTROLLER_MANAGER_ARGS=”–service_account_private_key_file=/tmp/serviceaccount.key”

From https://github.com/kubernetes/kubernetes/issues/11355#issuecomment-127378691

## Author

* **MuthuKumaran Elavarasu** - (https://github.com/MkumaranLakshmi)

