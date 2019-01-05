## Helm Repository 추가
``` bash
cloudctl login -u admin -p admin
helm init
helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
helm repo update
```

## Gogs 설치, Jenkins설치, Sonarqube설치
``` bash
helm install --name nfs-provisioner --namespace devops incubator/nfs-provisioner --tls
helm install --name devops-gogs --namespace devops incubator/gogs --tls
helm install --name devops-jenkins --namespace devops stable/jenkins --tls
helm install --name devops-sonarqube --namespace devops stable/sonarqube --tls
```

##
