---
layout: post
title: Kubernetes - create a test cluster
modified: 2019-06-03
categories: [k8s]
tags: 
  - testing
comments: true
---

I am using three machines on VMWare to start. <br/>
    83.16.16.71	    k8s1	master  <br/>
    83.16.16.72	    k8s2	node1   <br/>
    83.16.16.73	    k8s3	node2   <br/>

<h><b>Install:</b></h><br/>
Using ansible playbook downloaded from ansible galaxy saves a lot of time.

Inventory:
<pre class="prettyprint lang-sql linenums=1 ">
[k8s]
83.16.16.71 kubernetes_role=master hn=k8s1 
83.16.16.72 kubernetes_role=node hn=k8s2 
83.16.16.73 kubernetes_role=node hn=k8s3

[k8s:vars]
ansible_user=root
ping_target=8.8.8.8
</pre>

First, set up the environment, including hostname, disable firewall, SELinux, etc.

<pre class="prettyprint lang-sh linenums=1 ">
# k8s_env.yml
---
- hosts: all
  tasks:
  - name: dns
    lineinfile: 
      dest=/etc/resolv.conf
      regexp='^nameserver *8.8.8.8$' 
      line="nameserver 8.8.8.8"
      state=present
      backup=yes
    
  - name: test connection
    command: ping -c 3 {{ ping_target }}
    
  - name: hostname
    hostname:
      name: "{{hn}}.zhangqiaoc.com"
    
  - name: hosts
    lineinfile: 
      dest=/etc/hosts 
      regexp='.*{{ item }} .*' 
      line="{{ hostvars[item].ansible_default_ipv4.address }} {{hostvars[item]['ansible_nodename']}} {{hostvars[item]['ansible_hostname']}}" state=present
    when: hostvars[item].ansible_default_ipv4.address is defined
    with_items: "{{  groups.k8s }}"
    
  - name: yum update
    yum:
      name: '*'
      state: latest
      update_only: yes
    
  - name: firewall
    systemd:
      name: firewalld
      enabled: no
      state: stopped
      
  - name: Disable SWAP 1
    shell: |
      swapoff -a
    
  - name: Disable SWAP 2
    replace:
      path: /etc/fstab
      regexp: '^(.+ swap .*)$'
      replace: '# \1'
        
  - name: selinux
    selinux:
      state: disabled
    notify:
      reboot
    
  - name: meta
    meta: 
      flush_handlers
     
  handlers:
  - name: reboot
    reboot:
      reboot_timeout: 600    
</pre>

$ ansible-playbook -i hosts k8s_env.yml

next, install and config kubernetes.

<pre class="prettyprint lang-sql linenums=1 ">
# kubernetes.yml
---
- hosts: all
  vars:
    kubernetes_allow_pods_on_master: True
    pip_install_packages:
      - name: docker
      - name: awscli
      
  roles:
    - geerlingguy.repo-epel
    - geerlingguy.pip
    - geerlingguy.docker
    - geerlingguy.kubernetes
</pre>

$ ansible-playbook -i hosts kubernetes.yml

Now, we have a kubernetes cluster. </br>
Next, we can create an app for testing

<pre class="prettyprint lang-sql linenums=1 ">
# kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080

# kubectl expose deployment kubernetes-bootcamp --type=NodePort

# kubectl scale deployments/kubernetes-bootcamp --replicas=4

# kubectl get deployments
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   4/4     4            4           14h

# kubectl get pods -o wide
NAME                                   READY   STATUS    RESTARTS   AGE     IP            NODE                 
kubernetes-bootcamp                    1/1     Running   0          2m43s   10.244.1.8    k8s3.zhangqiaoc.com  
kubernetes-bootcamp-6bf84cb898-44xxm   1/1     Running   0          19s     10.244.1.9    k8s3.zhangqiaoc.com  
kubernetes-bootcamp-6bf84cb898-d7kgv   1/1     Running   0          19s     10.244.2.8    k8s2.zhangqiaoc.com  
kubernetes-bootcamp-6bf84cb898-ps77s   1/1     Running   0          69s     10.244.2.7    k8s2.zhangqiaoc.com  
kubernetes-bootcamp-6bf84cb898-rfwpl   1/1     Running   0          19s     10.244.1.10   k8s3.zhangqiaoc.com  

#  kubectl get services
NAME                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
kubernetes            ClusterIP   10.96.0.1        &lt;none&gt;        443/TCP          14h
kubernetes-bootcamp   NodePort    10.104.227.188   &lt;none&gt;        8080:31186/TCP   15s

# pod IP
# curl 10.244.1.8:8080
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp | v=1

# cluster IP
# curl 10.104.227.188:8080
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-6bf84cb898-d7kgv | v=1

# machine IP
# curl 83.16.16.72:31186
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-6bf84cb898-ps77s | v=1
</pre>

Now, we can use the physical IP address 83.16.16.72 to access the pods. But if this machine down, how to switch to another IP address?<br/>
We can use nginx as a load balancer. I add a new box, 83.16.16.70 to install nginx<br/>
In order to use yum to install nginx, we have to enable epel at first.

After install, configure load balancer:

<pre class="prettyprint lang-sql linenums=1 ">
# vi /etc/nginx/nginx.conf
# add
stream {
    server {
        listen 8002;
        proxy_pass bootcamp;
    }
    upstream bootcamp {
        server 83.16.16.72:31186;
        server 83.16.16.73:31186;
    }
}

# systemctl restart nginx
</pre>

Now, we can use nginx to access our application directly.
<pre class="prettyprint lang-sql linenums=1 ">
# curl 83.16.16.70:8002
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp | v=1
</pre>

Next, there is a problem that for every new deployment, we need to change nginx.conf manually, and sometimes, the load balancer is not under our control.

So, the better way is to use k8s ingress to expose only one port on every node to load balancer and use ingress to forward requests depending on the url.

create ingress 
<pre class="prettyprint lang-sql linenums=1 ">
# cat nginx-ingress-controller.yml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: default-http-backend
  labels:
    k8s-app: default-http-backend
  namespace: kube-system
spec:
  replicas: 1
  template:
    metadata:
      labels:
        k8s-app: default-http-backend
    spec:
      terminationGracePeriodSeconds: 60
      containers:
      - name: default-http-backend
        # Any image is permissable as long as:
        # 1. It serves a 404 page at /
        # 2. It serves 200 on a /healthz endpoint
        image: gcr.io/google_containers/defaultbackend:1.0
        livenessProbe:
          httpGet:
            path: /healthz
            port: 8080
            scheme: HTTP
          initialDelaySeconds: 30
          timeoutSeconds: 5
        ports:
        - containerPort: 8080
        resources:
          limits:
            cpu: 10m
            memory: 20Mi
          requests:
            cpu: 10m
            memory: 20Mi
---
apiVersion: v1
kind: Service
metadata:
  name: default-http-backend
  namespace: kube-system
  labels:
    k8s-app: default-http-backend
spec:
  ports:
  - port: 80
    targetPort: 8080
  selector:
    k8s-app: default-http-backend
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-ingress-controller
  labels:
    k8s-app: nginx-ingress-controller
  namespace: kube-system
spec:
  replicas: 1
  template:
    metadata:
      labels:
        k8s-app: nginx-ingress-controller
    spec:
      # hostNetwork makes it possible to use ipv6 and to preserve the source IP correctly regardless of docker configuration
      # however, it is not a hard dependency of the nginx-ingress-controller itself and it may cause issues if port 10254 already is taken on the host
      # that said, since hostPort is broken on CNI (https://github.com/kubernetes/kubernetes/issues/31307) we have to use hostNetwork where CNI is used
      # like with kubeadm
      serviceAccountName: nginx-ingress-serviceaccount
      hostNetwork: true
      terminationGracePeriodSeconds: 60
      containers:
      - image: gcr.io/google_containers/nginx-ingress-controller:0.9.0-beta.11
        name: nginx-ingress-controller
        readinessProbe:
          httpGet:
            path: /healthz
            port: 10254
            scheme: HTTP
        livenessProbe:
          httpGet:
            path: /healthz
            port: 10254
            scheme: HTTP
          initialDelaySeconds: 10
          timeoutSeconds: 1
        ports:
        - containerPort: 80
          hostPort: 80
        - containerPort: 443
          hostPort: 443
        env:
          - name: POD_NAME
            valueFrom:
              fieldRef:
                fieldPath: metadata.name
          - name: POD_NAMESPACE
            valueFrom:
              fieldRef:
                fieldPath: metadata.namespace
        args:
        - /nginx-ingress-controller
        - --default-backend-service=$(POD_NAMESPACE)/default-http-backend
</pre>

<pre class="prettyprint lang-sql linenums=1 ">
# cat rbac.yaml 
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nginx-ingress-serviceaccount
  namespace: kube-system
 
---
 
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: nginx-ingress-clusterrole
rules:
  - apiGroups:
      - ""
    resources:
      - configmaps
      - endpoints
      - nodes
      - pods
      - secrets
    verbs:
      - list
      - watch
  - apiGroups:
      - ""
    resources:
      - nodes
    verbs:
      - get
  - apiGroups:
      - ""
    resources:
      - services
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - "extensions"
    resources:
      - ingresses
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - ""
    resources:
        - events
    verbs:
        - create
        - patch
  - apiGroups:
      - "extensions"
    resources:
      - ingresses/status
    verbs:
      - update
 
---
 
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  name: nginx-ingress-role
  namespace: kube-system
rules:
  - apiGroups:
      - ""
    resources:
      - configmaps
      - pods
      - secrets
      - namespaces
    verbs:
      - get
  - apiGroups:
      - ""
    resources:
      - configmaps
    resourceNames:
      - "ingress-controller-leader-nginx"
    verbs:
      - get
      - update
  - apiGroups:
      - ""
    resources:
      - configmaps
    verbs:
      - create
  - apiGroups:
      - ""
    resources:
      - endpoints
    verbs:
      - get
      - create
 
---
 
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: nginx-ingress-role-nisa-binding
  namespace: kube-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: nginx-ingress-role
subjects:
  - kind: ServiceAccount
    name: nginx-ingress-serviceaccount
    namespace: kube-system
 
---
 
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: nginx-ingress-clusterrole-nisa-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: nginx-ingress-clusterrole
subjects:
  - kind: ServiceAccount
    name: nginx-ingress-serviceaccount
    namespace: kube-system
</pre>

<pre class="prettyprint lang-sql linenums=1 ">
# kubectl -n kube-system get deployments
NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
coredns                    2/2     2            2           15h
default-http-backend       1/1     1            1           14h
nginx-ingress-controller   1/1     1            1           14h

# kubectl -n kube-system get pods -o wide
NAME                                          READY   STATUS    RESTARTS   AGE     IP            NODE                  
coredns-86c58d9df4-gtrf5                      1/1     Running   2          15h     10.244.0.7    k8s1.zhangqiaoc.com   
coredns-86c58d9df4-xt4gq                      1/1     Running   2          15h     10.244.0.6    k8s1.zhangqiaoc.com   
default-http-backend-64c956bc67-jwxsd         1/1     Running   0          5h44m   10.244.0.8    k8s1.zhangqiaoc.com   
etcd-k8s1.zhangqiaoc.com                      1/1     Running   2          15h     83.16.16.71   k8s1.zhangqiaoc.com   
kube-apiserver-k8s1.zhangqiaoc.com            1/1     Running   2          15h     83.16.16.71   k8s1.zhangqiaoc.com   
kube-controller-manager-k8s1.zhangqiaoc.com   1/1     Running   3          15h     83.16.16.71   k8s1.zhangqiaoc.com   
kube-flannel-ds-amd64-496md                   1/1     Running   1          15h     83.16.16.73   k8s3.zhangqiaoc.com   
kube-flannel-ds-amd64-8trbt                   1/1     Running   1          15h     83.16.16.72   k8s2.zhangqiaoc.com   
kube-flannel-ds-amd64-hhn7z                   1/1     Running   2          15h     83.16.16.71   k8s1.zhangqiaoc.com   
kube-proxy-28kfg                              1/1     Running   1          15h     83.16.16.72   k8s2.zhangqiaoc.com   
kube-proxy-p65m8                              1/1     Running   2          15h     83.16.16.71   k8s1.zhangqiaoc.com   
kube-proxy-vqv64                              1/1     Running   1          15h     83.16.16.73   k8s3.zhangqiaoc.com   
kube-scheduler-k8s1.zhangqiaoc.com            1/1     Running   2          15h     83.16.16.71   k8s1.zhangqiaoc.com   
nginx-ingress-controller-59469ff966-4ddgp     1/1     Running   0          103m    83.16.16.71   k8s1.zhangqiaoc.com   
</pre>

create a ingress rule

<pre class="prettyprint lang-sql linenums=1 ">
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress
spec:
  rules:
  - host: www.zhangqiaoc.com
    http:
      paths:
      - backend:
          serviceName: kubernetes-bootcamp
          servicePort: 8080
        path: /bootcamp
</pre>

We need to add an entry on Nginx in 83.16.16.70
<pre class="prettyprint lang-sql linenums=1 ">
stream {
    server{               <--
        listen 8001;
        proxy_pass k8s;
    }
    server {
        listen 8002;
        proxy_pass bootcamp;
    }
    upstream k8s {        <--
        server 83.16.16.71:80;
        server 83.16.16.72:80;
        server 83.16.16.73:80;
    }
    upstream bootcamp {
        server 83.16.16.72:31186;
        server 83.16.16.73:31186;
    }
}
</pre>

<pre class="prettyprint lang-sql linenums=1 ">
$ curl www.zhangqiaoc.com:8001/bootcamp
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-6bf84cb898-ps77s | v=1
</pre>

But, if using IP address is not working.
<pre class="prettyprint lang-sql linenums=1 ">
$ curl 83.16.16.70:8001/bootcamp
default backend - 404
</pre>














