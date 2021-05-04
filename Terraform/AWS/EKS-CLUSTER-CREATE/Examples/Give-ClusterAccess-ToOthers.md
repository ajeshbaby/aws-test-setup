## Give Access to someone in team for your Kubernetes Cluster running on AWS  

When we have to share the deails of our clusters to someone else in the team:

1) share the kubeconfig file of that cluster from inside the .kube folder

2) make sure to make changes in configmap and add the ARN details of the user we want to give access to:
That can be taken from logging onto IAM,goto USERS, select the USER, copy the USER-ARN

`kubectl edit cm aws-auth -n kube-system`

```
  mapUsers: |
    - userarn: arn:aws:iam::555555555555:user/admin       ### Add the ARN ID of the user for whom the access needs to be given ##
      username: chandan
      groups:
        - system:masters

```


    
