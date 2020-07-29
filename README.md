# Eks-Task 
 Hi guys Taday we are going to see how Eks work and how can we launch our own kubernates cluster in just few clicks on aws.
* Prequisite aws account,basic knowledge how kubernates work,basics of aws.
* So first of all create IAM role on aws with admin access and configure aws cli we are going to launch all the things using aws cli we can launch with help og gui also but gui is not accesible all the time so its good to practice from cli.
* Once IAM is created and aws cli is configured download kubectl this link (https://kubernetes.io/docs/tasks/tools/install-kubectl/) and eksctl(https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html) once this is downlaod makke sure a=to ass them in enviroment variable so that it can be accesed from anywhere and once its done we are all set to launch our own Kubernates cluster.
* First clone this repo files and open cluster.yaml file and there you can change things according to your need such as cluster name and there os one term called node group in which we have to give information of type of node we want here i use t2.micro beacuse they are free you can set ng according to you need **Remember that eks is not free you have to pay something check pricing brfore doing anything**  once you cluster file is ready save it and close it.
* runt he following command ("eksctl create cluster -f filename")  its time to take grab a coffee :) it will take around 10 to 15 min.
* Now we ahve to make or upgrade  kube-coonfig file  so for that just run the followong command ("aws eks upadte-kubeconfig --name clustername").
* Once this is done now we are all set to to use k8s as it is we use in our own cluster or in minikube today in k8s we are going to launch wordpress and mysql  and we have design this in such a way that db will not ewxposed to anyone means it will use internal ip and wp is work as front end so that will be use load balancer service and guys here they use ELB service of AWS for laod balancing and for storage they by deafult use EBS but you can launch on EFS so that all node save data to one place.
* Foe running this setup you have to run the following command ("kubectl create -k .) here we are saying kubernates to run kuztamization.yml file its one file which contain secrets for database and here we set which file to run and when we never want forst our Wordpress is created and then Mysql what we want is fist mysql db is created and then Wordpress deploy on it so kuztamization file will take care of it.

 * Once pod is deployed you can see service by(" kubectl get svc") and  take domain of Wordpress and can see  your Wordpress site. 
 * Here you can  also use Helm to install helm run following cmnd:-
 * - ("helm repo add stable https://kubernetes-charts.storage.googleapis.com/
      
      helm repo list
      
      helm repo update)
* Once this is done we have to set tiller to set tiller run the followoing cmnd :-
* -("kubectl -n kube-system create serviceaccount tiller 

kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller 

helm init --service-account tiller

kubectl get pods --namespace kube-system

helm init --service-account tiller --upgrade")

* In my previous article i have shown how to launch promethesus and grafana  from yaml file if not see chek this out **https://www.linkedin.com/pulse/prometheus-grafana-on-kubernetes-aditya-gupta**.
* Today we are goiing to use helm package to launch prometheus and grafana  to do so run the following commands.

 * For Prometheus run  following command (" kubectl create namespace prometheus

helm install  stable/prometheus     --namespace prometheus     --set alertmanager.persistentVolume.storageClass="gp2"     --set server.persistentVolume.storageClass="gp2"

kubectl get svc -n prometheus

kubectl -n prometheus  port-forward svc/whimsical-markhor-prometheus-server  8888:80").

* For grafana  run following command ("kubectl create namespace grafana

helm install stable/grafana  --namespace grafana     --set persistence.storageClassName="gp2" --set adminPassword='GrafanaAdm!n'    --set datasources."datasources\.yaml".apiVersion=1     --set datasources."datasources\.yaml".datasources[0].name=Prometheus   --set datasources."datasources\.yaml".datasources[0].type=prometheus    --set datasources."datasources\.yaml".datasources[0].url=http://prometheus-server.prometheus.svc.cluster.local   --set datasources."datasources\.yaml".datasources[0].access=proxy     --set datasources."datasources\.yaml".datasources[0].isDefault=true  --set service.type=LoadBalancer

kubectl get  secret  fair-numbat-grafana   --namespace  grafana  -o yaml")
* This is how you can launch launch prometheus and grafana using helm and you can also lauch jenkins service on k8s same as i discuss this in my article **https://www.linkedin.com/pulse/jenkins-on-kubernetes-aditya-gupta**.
* Now we have seen there is one manual thing that we have to do is setting node grups we some time don't have much knowlege which node is required when or sometime we need more from that we plan so for that there is one service called fargat cluster and it will handle all the things by himslef even node also controlled by this service only thing you have to create fargat cluster and you are done rest other things will be taken care by  fargat service ( this service is sub service of ECS in aws).
* To launch fargat cluster   open fcluster .yml file and ther you can set name of cluster and namapsapce you want to use and once done save it and close it once this is done we are ready to laucnh fargat cluster run the following command("eksctl create cluster -f filename) o it will take time to launch the cluster you can check cluter is created or not by ("eksctl get cluster --region you region")
* once fargat cluster is created now we have to update kube config for that run  the following command (" aws eks --region your region update-kubeconfig  --name cluster name ") aftet this you can use kubectl get nodes to  check nodes launched by fargat cluster interanlly.


* Article link https://www.linkedin.com/pulse/eks-aditya-gupta*
