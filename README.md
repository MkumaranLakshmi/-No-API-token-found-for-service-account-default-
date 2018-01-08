# No API token found for service account "default"

[root@kubernetes-master pods]# kubectl create -f mysql.yaml 

Error from server (ServerTimeout): error when creating "mysql.yaml": No API token found for service account "default", retry after the token is automatically created and added to the service account

# Solve the certification problem:

Remove the certification

Create pod:

# kubectl create -f mysql.yaml
At this point the following error:

Error from server (ServerTimeout): error when creating "mysql.yaml": No API token found for service account "default", retry after the token is automatically created and added to the service account

The solution is to edit /etc/kubernetes/apiserver to remove SecurityContextDeny, ServiceAccount in KUBE_ADMISSION_CONTROL and restart the kube- apiserver.service service:

#vim /etc/kubernets/apiserver
KUBE_ADMISSION_CONTROL="--admission_control=NamespaceLifecycle,NamespaceExists,LimitRanger,ResourceQuota"

#systemctl restart kube-apiserver.service
After re-create pod:

# kubectl create -f mysql.yaml
pod "mysql" created


## Author

* **MuthuKumaran Elavarasu** - (https://github.com/MkumaranLakshmi)

