# 초기 설치
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

---
# Jenkins 설정
``` bash
### 네임스페이스에 저장되어 있는 secret 목록을 불러옵니다.
kubectl get secret -n devops
 
kubectl get secret -n devops devops-jenkins -o yaml
 
### secret에서 jenkins-admin-password정보를 복사합니다.
### jenkins-admin-password는 base64로 인코딩 되어 있습니다. 인코딩 되어 있는 부분을 decode해서 password정보를 얻습니다.
 
 
echo "jenkins-admin-password" | base64 --decode
```

---
#SonarQube연동
``` bash
stage('Inspection Code') {
  container('maven') {
      sh "mvn sonar:sonar \
            -Dsonar.host.url=  \
            -Dsonar.login= "
  }
}
```

---
#Slack 연동

## Slack 메세지 상태별 정의문
``` bash
def notifyStarted() {
    slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
}
 
def notifySuccessful() {
    slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
}
 
def notifyFailed() {
  slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
}
```

## Pipeline에 try/catch문
``` bash
    try{
      notifySuccessful()
      } catch(e) {
        currentBuild.result = "FAILED"
        notifyFailed()
    }
```

