---
layout: post
title: "Jenkins on Kubernetes"
date: 2019-5-30
categories:
  - cloud
  - devops
typora-root-url: ../../../blog
---
### 安装

```sh
[centos@k8s-1 ~]$ helm install stable/jenkins
NAME:   filled-eagle
LAST DEPLOYED: Thu Jun  6 13:26:38 2019
NAMESPACE: default
STATUS: DEPLOYED
RESOURCES:
==> v1/Deployment
NAME                  AGE
filled-eagle-jenkins  0s
==> v1/Pod(related)
NAME                                   READY  STATUS   RESTARTS  AGE
filled-eagle-jenkins-7b89475bfd-nq7p7  0/1    Pending  0         0s
==> v1/ConfigMap
NAME                        AGE
filled-eagle-jenkins        0s
filled-eagle-jenkins-tests  0s
==> v1/ServiceAccount
filled-eagle-jenkins  0s
==> v1/Role
filled-eagle-jenkins-schedule-agents  0s
==> v1/RoleBinding
filled-eagle-jenkins-schedule-agents  0s
==> v1/Secret
filled-eagle-jenkins  0s
==> v1/PersistentVolumeClaim
filled-eagle-jenkins  0s
==> v1/Service
filled-eagle-jenkins-agent  0s
filled-eagle-jenkins        0s
NOTES:
1. Get your 'admin' user password by running:
  printf $(kubectl get secret --namespace default filled-eagle-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
2. Get the Jenkins URL to visit by running these commands in the same shell:
  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        You can watch the status of by running 'kubectl get svc --namespace default -w filled-eagle-jenkins'
  export SERVICE_IP=$(kubectl get svc --namespace default filled-eagle-jenkins --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")
  echo http://$SERVICE_IP:8080/login
3. Login with the password from step 1 and the username: admin
For more information on running Jenkins on Kubernetes, visit:
https://cloud.google.com/solutions/jenkins-on-container-engine
```

上面挂载了一个磁盘卷，命名 "name": "jenkins-home”, "mountPath": "/var/jenkins_home”。 这个chart 有个缺点就是 jenkins 启动后，Some plugins could not be loaded due to unsatisfied dependencies. 安装报错，可能是因为我网络的问题。这些 plugin 安装后是放到 jenkins_home目录（也就是 PV）么？这样重启后这些 plugin 还在，是个不错的结果。`helm install bitnami/jenkins` 这个倒没有依赖错误，但是没有内置 cloud build engine plugin。 

Jenkins on Kubernetes 这个其实是一套方案，[Cloud Build](https://cloud.google.com/solutions/jenkins-on-container-engine) can be used from within your Jenkins jobs to build Docker images without needing to host your own Docker daemon. 也就是说是构建容器的方案。[配置构建执行程序](https://cloud.google.com/solutions/configuring-jenkins-kubernetes-engine)，还有点复杂。 

### 测试构建

具体安装好plugin后，会看到每次运行 job 都会起一个 jenkins/jnlp-slave pod：

```
Still waiting to schedule task
Waiting for next available executor
Agent default-dmn7k is provisioned from template Kubernetes Pod Template
Agent specification [Kubernetes Pod Template] (filled-eagle-jenkins-slave ):
* [jnlp] jenkins/jnlp-slave:3.27-1(resourceRequestCpu: 200m, resourceRequestMemory: 256Mi, resourceLimitCpu: 200m, resourceLimitMemory: 256Mi)
```

运行完就会关闭。这个可以在pod里面构建容器？docker in docker？ 

[这个样例代码](https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes/blob/320bf7b11cc3f4f9d74fe26a173f886574e2ef2f/sample-app/Jenkinsfile#L51)里面很有意思，agent的定义其实可以在jenkins系统配置里面web ui进行定义，如果系统配置里面定义了，就要设置executor数字为0，然后agent为any，这样就能match倒系统配置里面的kubernetes。agent里面Kubernetes的容器列表最后其实是运行在Pod里面的多个容器，比如主的jenkins/jnlp-slave和发布的kubectl pod，在pipeline里面调用pod里面容器用container('kubectl')方式，很炫酷啊，显示了容器的灵活之处。这块代码是在plugin java写的？ 

这里认证怎么办？一个是docker push的镜像仓库地址，一个是kubeconfig信息。尝试运行了`kubectl get nodes`，可以直接返回，这是哪里的默认权限？ 

[Docker in Docker?](https://itnext.io/docker-in-docker-521958d34efd) 其实太复杂，有兼容性问题，这里提供的方案是直接连Host上面的docker daemon。 

Kaniko is another option for users looking to build containers inside their clusters. Kaniko does not require a Docker daemon to be present in order to build and push images to a remote registry.这个是google推荐的，这个是怎么实现的呢？The kaniko executor image is responsible for building an image from a Dockerfile and pushing it to a registry. Within the executor image, we extract the filesystem of the base image (the FROM image in the Dockerfile). We then execute the commands in the Dockerfile, snapshotting the filesystem in userspace after each one. After each command, we append a layer of changed files to the base image (if there are any) and update image metadata. 直接操作镜像，不知道这样是否有兼容性问题。 

[使用Kaniko进行构建](https://flywzj.com/blog/kaniko/) kaniko不依赖于Docker守护程序，并且在用户空间中完全执行Dockerfile中的每个命令。 在kubernetes上构建还有另外一种选择，就是docker in docker，将宿主机的/var/run/docker.dock挂载到pod，并使用宿主机Docker守护程序执行构建。但是docker in docker必须运行在特权模式下，这会产生安全风险。我不禁想：jenkins X是用的什么技术呢？原来是tektoncd，这个很强，可以用自己格式定义pipeline CRD，[看了下](https://www.infoq.com/presentations/cloud-native-ci-cd-jenkins-knative/)，似乎build docker用的也是kaniko。看来我的方案最终要用的也是这个。jenkin x用这个，那自己的pipeline有啥用呢？感觉只是一个皮而已了。 

取消 Jenkins job 的时候还在运行的 Pod 会被 kill 掉，这个不错。 

创建一个 Pod 模板定义： https://github.com/GoogleContainerTools/kaniko#running-kaniko-in-a-kubernetes-cluster [an example of using Kaniko in Jenkins](https://github.com/jenkinsci/kubernetes-plugin/blob/kubernetes-1.14.5/examples/kaniko-declarative.groovy)  

push 到哪里去呢？既然这个 kaniko 支持 --insecure --skip-tls-verify，直接自己搭建一个？但是这样后续k8s安装不是也得相应配置。 

权限配置可以[通过ConfigMap传入](https://blog.ihypo.net/15487483292659.html)，但是我的一直报错： 

    MountVolume.SetUp failed for volume "volume-0" : configmap " docker-config" not found 

我用了个BusBox 测试，可以 mount，查看两者的yaml，并无不同。仔细查看，我上面" docker-config"多了个空格😂 还好错误信息这里用引号，否则是怎么也看不出来了（一个好的日志显示方式）。 **这里很重要：**根据yaml就能完全掌握一个Pod，而不是代码的细节有区别。yaml就是标准，这样不管pod是如何创建的（手工yaml或者程序yaml），这些都没有区别。yaml就是html，就是标准，运行结果就是渲染结果，一样的html，没有可能渲染结果不一样，不一样结果肯定是yaml不同。 

push镜像出现问题：

    error pushing image: failed to push to destination index.docker.io/fkpwolf/spring-boot-k8s-hpa:pp1: BLOB_UPLOAD_UNKNOWN: "blob upload unknown to registry" 

这个Dockerfile里面有两阶段，这个是怎么push的呢？先搞个直接运行kaniko的例子吧：
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug-539ddefcae3fd6b411a95982a830d987f4214251
    args: ["--dockerfile=/demo/Dockerfile",
            "--context=/demo",
            "--insecure=true",
            "--skip-tls-verify=true",
            "--destination=fkpwolf/spring-boot-k8s-hpa:pp1"]
    volumeMounts:
      - name: docker-config
        mountPath: /kaniko/.docker/
      - name: project-volume
        mountPath: /demo
  restartPolicy: Never
  volumes:
    - name: docker-config
      configMap:
        name: docker-config
    - name: project-volume
      hostPath:
        path: /home/centos/flask-demo
  nodeSelector:
    kubernetes.io/hostname: k8s-3-new
```

这里用到了hostPath作为直接读取的目录，这样不用git来下载，为了不用每个主机上面准备这个目录，用了nodeSelector来标明，这样我只用在这个节点上准备目录就可以。

还是一样的Push 失败。难道是我docker-config内容是错误的？删除这个卷，还是一样的错误，认证方式不对？好吧，我试试其他的 docker registry：

```shell
[centos@k8s-1 ~]$ helm install stable/docker-registry --namespace cicd --set persistence.enabled=true
NAME:   lumbering-starfish
LAST DEPLOYED: Sat Jun  8 13:05:07 2019
NAMESPACE: cicd
STATUS: DEPLOYED
RESOURCES:
==> v1/Service
NAME                                AGE
lumbering-starfish-docker-registry  0s
==> v1beta1/Deployment
lumbering-starfish-docker-registry  0s
==> v1/Pod(related)
NAME                                                 READY  STATUS   RESTARTS  AGE
lumbering-starfish-docker-registry-564bbc7457-rrmgl  0/1    Pending  0         0s
==> v1/Secret
NAME                                       AGE
lumbering-starfish-docker-registry-secret  0s
==> v1/ConfigMap
lumbering-starfish-docker-registry-config  0s
==> v1/PersistentVolumeClaim
lumbering-starfish-docker-registry  0s
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace cicd -l "app=docker-registry,release=lumbering-starfish" -o jsonpath="{.items[0].metadata.name}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl port-forward $POD_NAME 8080:5000
```

这个默认都不要用户名和密码，上面构建测试成功，看来还是 push 时认证的问题导致。 
* `curl 192.168.51.11:30704/v2/_catalog` 可以看到remote repository列表 
* `curl 192.168.51.11:30704/v2/spring-boot-k8s-hpa/tags/list` 列出所有tags 

[使用Kaniko进行构建](https://flywzj.com/blog/kaniko/) 这个是push到harbor上面，有配置证书。 

最后的 pipeline 定义为： 

```groovy
#!groovy
pipeline{
    agent any
    stages {
        stage('build'){
            steps {
                git 'https://github.com/learnk8s/spring-boot-k8s-hpa.git'
                container(name: 'kaniko', shell: '/busybox/sh') {
                    sh '''#!/busybox/sh
                    /kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=192.168.51.11:30704/spring-boot-k8s-hpa:pp2
                    '''
                }
            }
        }
        stage('deploy to k8s'){
            steps {
                echo "start to deploy"
                container('kubectl') {
                    sh("kubectl get nodes")
                } 
            }
        }
    }
}
```

下一步可以看看如果启用 docker registry 用户认证，pipeline 能否还能正常运行。

### Inside 

![](/images/2019/jenkins-on-k8s-running.png)

* Jenkins Pod 常驻运行，Agent Pod 动态创建，对应一次构建
* jnlp-slave 会上报 build 构建状态，如果 Jenkins Pod 和 jnlp-slave 通信没有问题，Jenkins Pod 也可以运行在 k8s 外面，就和一般企业部署方式一样
* Agent Pod 包含多个容器，在 Kubernetes plugin 中配置或者定义在 pipeline 里面

这种运行模式对已有系统改变很小，Jenkins 也可以直接在操作系统上面运行。

### 插件

<https://wiki.jenkins.io/display/JENKINS/Kubernetes+Plugin>

<https://github.com/jenkinsci/kubernetes-plugin>

* src/main/java/org/csanchez/jenkins/plugins/kubernetes/PodTemplate.java 这个是对应 web ui form 的pojo 
* src/main/java/org/csanchez/jenkins/plugins/kubernetes/KubernetesLauncher.java 启动 pod 
* src/main/java/org/csanchez/jenkins/plugins/kubernetes/KubernetesSlave.java 调用 k8s，用的是io.fabric8.kubernetes.client.KubernetesClient 
* src/main/java/org/csanchez/jenkins/plugins/kubernetes/KubernetesFactoryAdapter.java 连接 k8s，用的是fabric8的ConfigBuilder和DefaultKubernetesClient类，https://github.com/fabric8io/kubernetes-client This client provides access to the full Kubernetes & OpenShift 3 REST APIs via a fluent DSL，Java 的客户端，能和 go-client比较么？ 

[fabric8](http://fabric8.io/) 是一个开源集成开发平台，为基于Kubernetes和Jenkins的微服务提供持续发布，也是一个蛮大的 java 微服务平台，似乎已经停止开发了。也是 RedHat 的项目，可惜了啊。 