# aws-EKS-title-game-2048:


## prerequisites to start the project step-by-step:

### 1 .Kubectl:

you have to required kubectl installed in your local-machine to install just click the link----> (https://kubernetes.io/docs/tasks/tools/)



### 

### 2\. EKSctl(elastic Kubernetes service) for AWS:

you have to required EKSctl installed in your local-machine to install just click the

link---> (https://docs.aws.amazon.com/eks/latest/eksctl/installation.html)





### 3\. AWS CLI:

you have to install AWS CLI to communicate with AWS services/resources to install just click the link----> (https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

"After installing AWS CLI configure with your AWS account "

**To configure with AWS Account provide access key from security credentials in AWS Account"**



### Steps to deploy Game-App in EKS(elastic Kubernetes services):

### 

##### -->> **TO create cluster using Fargate**-

    Install using Fargate

    command: (eksctl create cluster --name demo-cluster --region us-east-1 --fargate)







##### -->>After creating cluster then update kubeconfig-

    command: (aws eks update-kubeconfig --name <cluster name> --region <us-east-1>)





##### -->>Now create fargate profile-

    command: (eksctl create fargateprofile \
    --cluster <cluster-name> \
    --region us-east-1 \
    --name alb-sample-app \
    --namespace game-2048)




##### --->>Deploy the deployment, service and Ingress-

    command: (kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048\_full.yaml)

    it deploys pods(container) required for the application.

    To check wheather the pods are created or not<> use command:(kubectl get pods -n game-2048)

    To check wtheather the service is created or not <> use command:(kubectl get svc -n game-2048)

    To check wtheather the ingress is created or not <> use command:(kubectl get ingress -n game-2048)







##### --->> To deploy ALB(Application Load Balancer)

    Configure with OIDC(OIDC IS bridge that allows pods to access AWS services securely)

    TO configure commands are (export cluster\_name=<cluster-name>) next (eksctl utils associate-iam-oidc-provider --cluster <cluster\_name> --approve)







##### --->> Inorder to deploy ALB create IAM(identity Access Management) policy:



###### &nbsp;   download IAM policy: 

(curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam\_policy.json)



###### &nbsp;   Create IAM policy :
(aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json)



###### &nbsp;   then Create IAM Role :
 (eksctl create iamserviceaccount \
    --cluster=<your-cluster-name> \
    --namespace=kube-system \
    --name=aws-load-balancer-controller \
    --role-name AmazonEKSLoadBalancerControllerRole \
    --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
    --approve)









##### --->>Install Helm charts:

&nbsp;   to download and install use this command(curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash)



&nbsp;   then to check the version(helm version)



&nbsp;   now Add helm repo command (helm repo add eks https://aws.github.io/eks-charts)

   Now to depoly load balancer with helm command :
   (helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<your-region> \
  --set vpcId=<your-vpc-id> )

   

&nbsp;  Verify that the deployments are running command (kubectl get deployment -n kube-system aws-load-balancer-controller)







##### --->>Now to access application in external website use dns name of cluster :

&nbsp;    or use this command (kubectl get ingress -n game-2048) you will address copy that address and past in any browser







##### --->>If your account cannot create loadbalancer you can run it in your localhost to check :



&nbsp;     use this command to port to NodePort mode (kubectl port-forward svc/service-2048 8080:80 -n game-2048) then in browser type (http://localhost:8080)



&nbsp; 


