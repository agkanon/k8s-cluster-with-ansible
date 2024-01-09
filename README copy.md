# Kubernetes 
01 ***SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE***

For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


We have created a service account called green-sa-cka22-arch, a cluster role called green-role-cka22-arch and a cluster role binding called green-role-binding-cka22-arch.


Update the permissions of this service account so that it can only get all the namespaces in cluster1.

**ANSWER** ->

Edit the green-role-cka22-arch to update permissions:

student-node ~ ➜  kubectl edit clusterrole green-role-cka22-arch --context cluster1



At the end add below code:
```
- apiGroups:
  - "*"
  resources:
  - namespaces
  verbs:
  - get
```


You can verify it as below:

student-node ~ ➜  kubectl auth can-i get namespaces --as=system:serviceaccount:default:green-sa-cka22-arch
yes

02 ***SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE***

For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


Run a pod called looper-cka16-arch using the busybox image that runs the while loop while true; do echo hello; sleep 10;done. This pod should be created in the default namespace.

**ANSWER** ->

Create the pod definition:

student-node ~ ➜  vi looper-cka16-arch.yaml


```
##Content should be:

apiVersion: v1
kind: Pod
metadata:
  name: looper-cka16-arch
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["/bin/sh", "-c", "while true; do echo hello; sleep 10;done"]
```
Create the pod:

student-node ~ ➜  kubectl apply -f looper-cka16-arch.yaml --context cluster3

03 ***SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE***

For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


A pod called color-app-cka13-arch has been created in the default namespace. This pod logs can be accessed using kubectl logs -f color-app-cka13-arch command from the student-node. It is currently displaying Color is pink output. Update the pod definition file to make use of the environment variable with the value - green and recreate this pod.

**ANSWER** ->

Export the current pod definition:

student-node ~ ➜  kubectl get pod color-app-cka13-arch -o yaml > /tmp/color-app-cka13-arch.yaml



Edit the pod definition file to make the required changes:

student-node ~ ➜ vi /tmp/color-app-cka13-arch.yaml



Under env: -> - name: APP_COLOR change value: pink to value: green


Replace the pod:

student-node ~ ➜ kubectl replace -f /tmp/color-app-cka13-arch.yaml --force

pod "color-app-cka13-arch" deleted
pod/color-app-cka13-arch replaced

04 ***SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE***

For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


A pod called logger-cka03-arch has been created in the default namespace. Inspect this pod and save ALL INFO and ERROR's to the file /root/logger-cka03-arch-all on the student-node.

**ANSWER** ->

Run the command kubectl logs logger-cka03-arch --context cluster3 > /root/logger-cka03-arch-all on the student-node.

Run the command :
```
student-node ~ ➜ kubectl logs logger-cka03-arch --context cluster3 > /root/logger-cka03-arch-all

student-node ~ ➜  head /root/logger-cka03-arch-all
INFO: Wed Oct 19 10:38:57 UTC 2022 Logger is running
ERROR: Wed Oct 19 10:38:57 UTC 2022 Logger encountered errors!
INFO: Wed Oct 19 10:38:57 UTC 2022 Logger is running
ERROR: Wed Oct 19 10:38:57 UTC 2022 Logger encountered errors!
INFO: Wed Oct 19 10:38:57 UTC 2022 Logger is running
ERROR: Wed Oct 19 10:38:57 UTC 2022 Logger encountered errors!
INFO: Wed Oct 19 10:38:57 UTC 2022 Logger is running
ERROR: Wed Oct 19 10:38:57 UTC 2022 Logger encountered errors!
INFO: Wed Oct 19 10:38:57 UTC 2022 Logger is running
ERROR: Wed Oct 19 10:38:57 UTC 2022 Logger encountered errors!

student-node ~ ➜  
```
05 ***SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE***

For this question, please set the context to cluster1 by running:

kubectl config use-context cluster1

Create a generic secret called db-user-pass-cka17-arch in the default namespace on cluster1 using the contents of the file /opt/db-user-pass on the student-node

**ANSWER** ->

Create the required secret:

student-node ~ ➜  kubectl create secret generic db-user-pass-cka17-arch --from-file=/opt/db-user-pass

***SECTION: TROUBLESHOOTING***

For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


The blue-dp-cka09-trb deployment is having 0 out of 1 pods running. Fix the issue to make sure that pod is up and running.

**ANSWER** ->

List the pods

kubectl get pod

Most probably you see Init:Error or Init:CrashLoopBackOff for the corresponding pod.

Look into the logs

kubectl logs blue-dp-cka09-trb-xxxx -c init-container

You will see an error something like

sh: can't open 'echo 'Welcome!'': No such file or directory
Edit the deployment

kubectl edit deploy blue-dp-cka09-trb

Under initContainers: -> - command: add -c to the next line of - sh, so final command should look like this
   initContainers:
   - command:
     - sh
     - -c
     - echo 'Welcome!'
If you will check pod then it must be failing again but with different error this time, let's find that out

kubectl get event --field-selector involvedObject.name=blue-dp-cka09-trb-xxxxx

You will see an error something like

Warning   Failed      pod/blue-dp-cka09-trb-69dd844f76-rv9z8   Error: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: error during container init: error mounting "/var/lib/kubelet/pods/98182a41-6d6d-406a-a3e2-37c33036acac/volumes/kubernetes.io~configmap/nginx-config" to rootfs at "/etc/nginx/nginx.conf": mount /var/lib/kubelet/pods/98182a41-6d6d-406a-a3e2-37c33036acac/volumes/kubernetes.io~configmap/nginx-config:/etc/nginx/nginx.conf (via /proc/self/fd/6), flags: 0x5001: not a directory: unknown

Edit the deployment again

kubectl edit deploy blue-dp-cka09-trb

Under volumeMounts: -> - mountPath: /etc/nginx/nginx.conf -> name: nginx-config add subPath: nginx.conf and save the changes.
Finally the pod should be in running state.


***SECTION: TROUBLESHOOTING***

For this question, please set the context to cluster2 by running:


kubectl config use-context cluster2


We recently deployed a DaemonSet called logs-cka26-trb under kube-system namespace in cluster2 for collecting logs from all the cluster nodes including the controlplane node. However, at this moment, the DaemonSet is not creating any pod on the controlplane node.


Troubleshoot the issue and fix it to make sure the pods are getting created on all nodes including the controlplane node.

**ANSWER**

Check the status of DaemonSet

kubectl --context2 cluster2 get ds logs-cka26-trb -n kube-system
You will find that DESIRED CURRENT READY etc have value 2 which means there are two pods that have been created. You can check the same by listing the PODs

kubectl --context2 cluster2 get pod  -n kube-system
You can check on which nodes these are created on

kubectl --context2 cluster2 get pod <pod-name> -n kube-system -o wide
Under NODE you will find the node name, so we can see that its not scheduled on the controlplane node which is because it must be missing the reqiured tolerations. Let's edit the DaemonSet to fix the tolerations

kubectl --context2 cluster2 edit ds logs-cka26-trb -n kube-system
Under tolerations: add below given tolerations as well
```
- key: node-role.kubernetes.io/control-plane
  operator: Exists
  effect: NoSchedule
```
Wait for some time PODs should schedule on all nodes now including the controlplane node.


***SECTION: TROUBLESHOOTING***

For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


​A pod called nginx-cka01-trb is running in the default namespace. There is a container called nginx-container running inside this pod that uses the image nginx:latest. There is another sidecar container called logs-container that runs in this pod.

For some reason, this pod is continuously crashing. Identify the issue and fix it. Make sure that the pod is in a running state and you are able to access the website using the curl http://kodekloud-exam.app:30001 command on the controlplane node of cluster1.

**ANSWER**

Check the container logs:

kubectl logs -f nginx-cka01-trb -c nginx-container
You can see that its not able to pull the image.

Edit the pod
kubectl edit pod nginx-cka01-trb -o yaml
    
Change image tag from nginx:latst to nginx:latest
Let's check now if the POD is in Running state

kubectl get pod
You will notice that its still crashing, so check the logs again:

kubectl logs -f nginx-cka01-trb -c nginx-container
From the logs you will notice that nginx-container is looking good now so it might be the sidecar container that is causing issues. Let's check its logs.

kubectl logs -f nginx-cka01-trb -c logs-container
You will see some logs as below:

cat: can't open '/var/log/httpd/access.log': No such file or directory
cat: can't open '/var/log/httpd/error.log': No such file or directory
Now, let's look into the sidecar container

kubectl get pod nginx-cka01-trb -o yaml
Under containers: check the command: section, this is the command which is failing. If you notice its looking for the logs under /var/log/httpd/ directory but the mounted volume for logs is /var/log/nginx (under volumeMounts:). So we need to fix this path:

kubectl get pod nginx-cka01-trb -o yaml > /tmp/test.yaml
vi /tmp/test.yaml
Under command: change /var/log/httpd/access.log and /var/log/httpd/error.log to /var/log/nginx/access.log and /var/log/nginx/error.log respectively.

Delete the existing POD now:

kubectl delete pod nginx-cka01-trb
Create new one from the template

kubectl apply -f /tmp/test.yaml
Let's check now if the POD is in Running state

kubectl get pod
It should be good now. So let's try to access the app.

curl http://kodekloud-exam.app:30001
You will see error

curl: (7) Failed to connect to kodekloud-exam.app port 30001: Connection refused
So you are not able to access the website, et's look into the service configuration.

Edit the service
kubectl edit svc nginx-service-cka01-trb -o yaml 
Change app label under selector from httpd-app-cka01-trb to nginx-app-cka01-trb
You should be able to access the website now.
curl http://kodekloud-exam.app:30001


***SECTION: TROUBLESHOOTING***

For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


The purple-app-cka27-trb pod is an nginx based app on the container port 80. This app is exposed within the cluster using a ClusterIP type service called purple-svc-cka27-trb.


There is another pod called purple-curl-cka27-trb which continuously monitors the status of the app running within purple-app-cka27-trb pod by accessing the purple-svc-cka27-trb service using curl.


Recently we started seeing some errors in the logs of the purple-curl-cka27-trb pod.


Dig into the logs to identify the issue and make sure it is resolved.


Note: You will not be able to access this app directly from the student-node but you can exec into the purple-app-cka27-trb pod to check.


**ANSWER** ->

Check the purple-curl-cka27-trb pod logs

kubectl logs purple-curl-cka27-trb

You will see some logs as below

Not able to connect to the nginx app on http://purple-svc-cka27-trb

Now to debug let's try to access this app from within the purple-app-cka27-trb pod


kubectl exec -it purple-app-cka27-trb -- bash

curl http://purple-svc-cka27-trb

exit

You will notice its stuck, so app is not reachable. Let's look into the service to see its configured correctly.


kubectl edit svc purple-svc-cka27-trb
Under ports: -> port: and targetPort: is set to 8080 but nginx default port is 80 so change 8080 to 80 and save the changes

Let's check the logs now

kubectl logs purple-curl-cka27-trb
You will see Thank you for using nginx. in the output now.

***SECTION: TROUBLESHOOTING***

For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


One of the nginx based pod called cyan-pod-cka28-trb is running under cyan-ns-cka28-trb namespace and it is exposed within the cluster using cyan-svc-cka28-trb service.

This is a restricted pod so a network policy called cyan-np-cka28-trb has been created in the same namespace to apply some restrictions on this pod.


Two other pods called cyan-white-cka28-trb and cyan-black-cka28-trb are also running in the default namespace.


The nginx based app running on the cyan-pod-cka28-trb pod is exposed internally on the default nginx port (80).


Expectation: This app should only be accessible from the cyan-white-cka28-trb pod.


Problem: This app is not accessible from anywhere.


Troubleshoot this issue and fix the connectivity as per the requirement listed above.


Note: You can exec into cyan-white-cka28-trb and cyan-black-cka28-trb pods and test connectivity using the curl utility.


You may update the network policy, but make sure it is not deleted from the cyan-ns-cka28-trb namespace.

**ANSWER** ->

Let's look into the network policy

kubectl edit networkpolicy cyan-np-cka28-trb -n cyan-ns-cka28-trb

Under spec: -> egress: you will notice there is not cidr: block has been added, since there is no restrcitions on egress traffic so we 
can update it as below. Further you will notice that the port used in the policy is 8080 but the app is running on default port which is 80 so let's update this as well (under egress and ingress):

```
Change port: 8080 to port: 80
- ports:
  - port: 80
    protocol: TCP
  to:
  - ipBlock:
      cidr: 0.0.0.0/0
```      
Now, lastly notice that there is no POD selector has been used in ingress section but this app is supposed to be accessible from cyan-white-cka28-trb pod under default namespace. So let's edit it to look like as below:

```
ingress:
- from:
  - namespaceSelector:
      matchLabels:
        kubernetes.io/metadata.name: default
   podSelector:
      matchLabels:
        app: cyan-white-cka28-trb 
```
Now, let's try to access the app from cyan-white-pod-cka28-trb

kubectl exec -it cyan-white-cka28-trb -- sh

curl cyan-svc-cka28-trb.cyan-ns-cka28-trb.svc.cluster.local

Also make sure its not accessible from the other pod(s)

kubectl exec -it cyan-black-cka28-trb -- sh
curl cyan-svc-cka28-trb.cyan-ns-cka28-trb.svc.cluster.local

It should not work from this pod. So its looking good now.

***SECTION: TROUBLESHOOTING***

For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


The db-deployment-cka05-trb deployment is having 0 out of 1 PODs ready.


Figure out the issues and fix the same but make sure that you do not remove any DB related environment variables from the deployment/pod.

**ANSWER** ->

Find out the name of the DB POD:

kubectl get pod

Check the DB POD logs:

kubectl logs <pod-name>
You might see something like as below which is not that helpful:

Error from server (BadRequest): container "db" in pod "db-deployment-cka05-trb-7457c469b7-zbvx6" is waiting to start: CreateContainerConfigError

So let's look into the kubernetes events for this pod:

kubectl get event --field-selector involvedObject.name=<pod-name>

You will see some errors as below:

Error: couldn't find key db in Secret default/db-cka05-trb

Now let's look into all secrets:

kubectl get secrets db-root-pass-cka05-trb -o yaml
kubectl get secrets db-user-pass-cka05-trb -o yaml
kubectl get secrets db-cka05-trb -o yaml
Now let's look into the deployment.

Edit the deployment

kubectl edit deployment db-deployment-cka05-trb -o yaml

You will notice that some of the keys are different what are reffered in the deployment.

Change some env keys: db to database , db-user to username and db-password to password

Change a secret reference: db-user-cka05-trb to db-user-pass-cka05-trb

Finally save the changes.


***SECTION: TROUBLESHOOTING***

For this question, please set the context to cluster4 by running:


kubectl config use-context cluster4


On cluster4 we are having some weird issue where we are intermittently getting below error while running kubectl commands.


The connection to the server cluster4-controlplane:6443 was refused - did you specify the right host or port?
Whenever you get this error, you can wait for 10-15 seconds to make kubectl command work again, but it will come again after few second


We also noticed that kube-controller-manager-cluster4-controlplane pod is restarting continuously. Look into the issue and troubleshoot the same.


You can SSH into the cluster4 using ssh cluster4-controlplane command.

***SECTION: TROUBLESHOOTING***

For this question, please set the context to cluster2 by running:


kubectl config use-context cluster2


The cat-cka22-trb pod is stuck in Pending state. Look into the issue to fix the same. Make sure that the pod is in running state and its stable (i.e not restarting or crashing).


Note: Do not make any changes to the pod (No changes to pod config but you may destory and re-create).

**ANSWER** ->

Let's check the POD status
kubectl get pod

You will see that cat-cka22-trb pod is stuck in Pending state. So let's try to look into the events

kubectl --context cluster2 get event --field-selector involvedObject.name=cat-cka22-trb

You will see some logs as below

Warning   FailedScheduling   pod/cat-cka22-trb   0/3 nodes are available: 1 node(s) had untolerated taint {node-role.kubernetes.io/master: }, 2 node(s) didn't match Pod's node affinity/selector. preemption: 0/2 nodes are available: 3 Preemption is not helpful for scheduling.
So seems like this POD is using the node affinity, let's look into the POD to understand the node affinity its using.

kubectl --context cluster2 get pod cat-cka22-trb -o yaml

Under affinity: you will see its looking for key: node and values: cluster2-node02 so let's verify if node01 has these labels applied.

kubectl --context cluster2 get node cluster2-node01 -o yaml

Look under labels: and you will not find any such label, so let's add this label to this node.

kubectl label node cluster1-node01 node=cluster2-node01

Check again the node details

kubectl get node cluster2-node01 -o yaml

The new label should be there, let's see if POD is scheduled now on this node

kubectl --context cluster2 get pod

Its is but it must be crashing or restarting, so let's look into the pod logs

kubectl --context cluster2 logs -f cat-cka22-trb

You will see logs as below:

The HOST variable seems incorrect, it must be set to kodekloud
Let's look into the POD env variables to see if there is any HOST env variable

kubectl --context cluster2 get pod -o yaml

```Under env: you will see this

env:
- name: HOST
  valueFrom:
    secretKeyRef:
      key: hostname
      name: cat-cka22-trb
```      
So we can see that HOST variable is defined and its value is being retrieved from a secret called "cat-cka22-trb". Let's look into this secret

kubectl --context cluster2 get secret
kubectl --context cluster2 get secret cat-cka22-trb -o yaml

You will find a key/value pair under data:, let's try to decode it to see its value:


```echo "<the decoded value you see for hostname" | base64 -d```

ok so the value is set to kodekloude which is incorrect as it should be set to kodekloud. So let's update the secret:


```echo "kodekloud" | base64```

kubectl edit secret cat-cka22-trb

```Change requests storage hostname: a29kZWtsb3Vkdg== to hostname: a29kZWtsb3VkCg== (values may vary)```

POD should be good now.

***SECTION: SCHEDULING***

For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


Create a new deployment called ocean-tv-wl09 in the default namespace using the image kodekloud/webapp-color:v1.
Use the following specs for the deployment:


1. Replica count should be 3.

2. Set the Max Unavailable to 40% and Max Surge to 55%.

3. Create the deployment and ensure all the pods are ready.

4. After successful deployment, upgrade the deployment image to kodekloud/webapp-color:v2 and inspect the deployment rollout status.

5. Check the rolling history of the deployment and on the student-node, save the current revision count number to the /opt/revision-count.txt file.

6. Finally, perform a rollback and revert back the deployment image to the older version.

**ANSWER** ->

Set the correct context: -

kubectl config use-context cluster1


Use the following template to create a deployment called ocean-tv-wl09: -
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: ocean-tv-wl09
  name: ocean-tv-wl09
spec:
  replicas: 3
  selector:
    matchLabels:
      app: ocean-tv-wl09
  strategy: 
   type: RollingUpdate
   rollingUpdate:
     maxUnavailable: 40%
     maxSurge: 55%
  template:
    metadata:
      labels:
        app: ocean-tv-wl09
    spec:
      containers:
      - image: kodekloud/webapp-color:v1
        name: webapp-color
```

Now, create the deployment by using the kubectl create -f command in the default namespace: -

kubectl create -f <FILE-NAME>.yaml


After sometime, upgrade the deployment image to kodekloud/webapp-color:v2: -

kubectl set image deploy ocean-tv-wl09 webapp-color=kodekloud/webapp-color:v2


And check out the rollout history of the deployment ocean-tv-wl09: -

kubectl rollout history deploy ocean-tv-wl09

deployment.apps/ocean-tv-wl09 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>


NOTE: - Revision count is 2. In your lab, it could be different.



On the student-node, store the revision count to the given file: -

echo "2" > /opt/revision-count.txt


In final task, rollback the deployment image to an old version: -

kubectl rollout undo deployment ocean-tv-wl09


Verify the image name by using the following command: -

kubectl describe deploy ocean-tv-wl09


It should be kodekloud/webapp-color:v1 image.

***SECTION: SCHEDULING***

For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


Create a deployment called app-wl01 using the nginx image and scale the application pods to 2.

**ANSWER** ->

Run the command to change the context: -

kubectl config use-context cluster1


Run the following commands: -

kubectl create deployment app-wl01 --image=nginx --replicas=2


To cross-verify the deployed resources, run the commands as follows: -

kubectl get pods,deployments

***SECTION: SCHEDULING***

For this question, please set the context to cluster3 by running:

kubectl config use-context cluster3

We have deployed a 2-tier web application on the cluster3 nodes in the canara-wl05 namespace. However, at the moment, the web app pod cannot establish a connection with the MySQL pod successfully.


You can check the status of the application from the terminal by running the curl command with the following syntax:

curl http://cluster3-controlplane:NODE-PORT


To make the application work, create a new secret called db-secret-wl05 with the following key values: -

1. DB_Host=mysql-svc-wl05
2. DB_User=root
3. DB_Password=password123


Next, configure the web application pod to load the new environment variables from the newly created secret.


Note: Check the web application again using the curl command, and the status of the application should be success.


You can SSH into the cluster3 using ssh cluster3-controlplane command.

**ANSWER** ->

Set the correct context: -

kubectl config use-context cluster3
List the nodes: -

kubectl get nodes -o wide
Run the curl command to know the status of the application as follows: -

ssh cluster3-controlplane
```
curl http://10.17.63.11:31020
<!doctype html>
<title>Hello from Flask</title>
...

    <img src="/static/img/failed.png">
    <h3> Failed connecting to the MySQL database. </h3>


    <h2> Environment Variables: DB_Host=Not Set; DB_Database=Not Set; DB_User=Not Set; DB_Password=Not Set; 2003: Can&#39;t connect to MySQL server on &#39;localhost:3306&#39; (111 Connection refused) </h2>
```

As you can see, the status of the application pod is failed.


NOTE: - In your lab, IP addresses could be different.



Let's create a new secret called db-secret-wl05 as follows: -

```kubectl create secret generic db-secret-wl05 -n canara-wl05 --from-literal=DB_Host=mysql-svc-wl05 --from-literal=DB_User=root --from-literal=DB_Password=password123```
After that, configure the newly created secret to the web application pod as follows: -
```
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: webapp-pod-wl05
  name: webapp-pod-wl05
  namespace: canara-wl05
spec:
  containers:
  - image: kodekloud/simple-webapp-mysql
    name: webapp-pod-wl05
    envFrom:
    - secretRef:
        name: db-secret-wl05 
```
then use the kubectl replace command: -

kubectl replace -f <FILE-NAME> --force


In the end, make use of the curl command to check the status of the application pod. The status of the application should be success.
```
curl http://10.17.63.11:31020

<!doctype html>
<title>Hello from Flask</title>
<body style="background: #39b54b;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">


    <img src="/static/img/success.jpg">
    <h3> Successfully connected to the MySQL database.</h3>
```

***SECTION: STORAGE***

For this question, please set the context to cluster1 by running:

kubectl config use-context cluster1

Create a storage class with the name banana-sc-cka08-str as per the properties given below:


- Provisioner should be kubernetes.io/no-provisioner,

- Volume binding mode should be WaitForFirstConsumer.

- Volume expansion should be enabled.

**ANSWER** ->

Create a yaml template as below:
```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: banana-sc-cka08-str
provisioner: kubernetes.io/no-provisioner
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer 
```
Apply the template:
kubectl apply -f <template-file-name>.yaml

***SECTION: STORAGE***

For this question, please set the context to cluster1 by running:

kubectl config use-context cluster1

Create a storage class called orange-stc-cka07-str as per the properties given below:


- Provisioner should be kubernetes.io/no-provisioner.

- Volume binding mode should be WaitForFirstConsumer.


Next, create a persistent volume called orange-pv-cka07-str as per the properties given below:


- Capacity should be 150Mi.

- Access mode should be ReadWriteOnce.

- Reclaim policy should be Retain.

- It should use storage class orange-stc-cka07-str.

- Local path should be /opt/orange-data-cka07-str.

- Also add node affinity to create this value on cluster1-controlplane.


Finally, create a persistent volume claim called orange-pvc-cka07-str as per the properties given below:


- Access mode should be ReadWriteOnce.

- It should use storage class orange-stc-cka07-str.

- Storage request should be 128Mi.

- The volume should be orange-pv-cka07-str.

**ANSWER** ->

Create a yaml file as below:
```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: orange-stc-cka07-str
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer

---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: orange-pv-cka07-str
spec:
  capacity:
    storage: 150Mi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: orange-stc-cka07-str
  local:
    path: /opt/orange-data-cka07-str
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - cluster1-controlplane

---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: orange-pvc-cka07-str
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: orange-stc-cka07-str
  volumeName: orange-pv-cka07-str
  resources:
    requests:
      storage: 128Mi
Apply the template:
kubectl apply -f <template-file-name>.yaml 
```

***SECTION: SERVICE NETWORKING***

For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


Create a ReplicaSet with name checker-cka10-svcn in ns-12345-svcn namespace with image registry.k8s.io/e2e-test-images/jessie-dnsutils:1.3.


Make sure to specify the below specs as well:


command sleep 3600
replicas set to 2
container name: dns-image



Once the checker pods are up and running, store the output of the command nslookup kubernetes.default from any one of the checker pod into the file /root/dns-output-12345-cka10-svcn on student-node.

**ANSWER** ->

Change to the cluster4 context before attempting the task:

kubectl config use-context cluster3

Create the ReplicaSet as per the requirements:


```
kubectl apply -f - << EOF
---
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: null
  name: ns-12345-svcn
spec: {}
status: {}

---
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: checker-cka10-svcn
  namespace: ns-12345-svcn
  labels:
    app: dns
    tier: testing
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: testing
  template:
    metadata:
      labels:
        tier: testing
    spec:
      containers:
      - name: dns-image
        image: registry.k8s.io/e2e-test-images/jessie-dnsutils:1.3
        command:
          - sleep
          - "3600"
EOF
```


Now let's test if the nslookup command is working :


student-node ~ ➜  k get pods -n ns-12345-svcn 
NAME                       READY   STATUS    RESTARTS   AGE
checker-cka10-svcn-d2cd2   1/1     Running   0          12s
checker-cka10-svcn-qj8rc   1/1     Running   0          12s

student-node ~ ➜  POD_NAME=`k get pods -n ns-12345-svcn --no-headers | head -1 | awk '{print $1}'`

student-node ~ ➜  kubectl exec -n ns-12345-svcn -i -t $POD_NAME -- nslookup kubernetes.default
;; connection timed out; no servers could be reached

command terminated with exit code 1

There seems to be a problem with the name resolution. Let's check if our coredns pods are up and if any service exists to reach them:

student-node ~ ➜  k get pods -n kube-system | grep coredns
coredns-6d4b75cb6d-cprjz                        1/1     Running   0             42m
coredns-6d4b75cb6d-fdrhv                        1/1     Running   0             42m

student-node ~ ➜  k get svc -n kube-system 
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   62m



Everything looks okay here but the name resolution problem exists, let's see if the kube-dns service have any active endpoints:

student-node ~ ➜  kubectl get ep -n kube-system kube-dns 
NAME       ENDPOINTS   AGE
kube-dns   <none>      63m



Finally, we have our culprit.


If we dig a little deeper, we will it is using wrong labels and selector:

student-node ~ ➜  kubectl describe svc -n kube-system kube-dns 
Name:              kube-dns
Namespace:         kube-system
....
Selector:          k8s-app=core-dns
Type:              ClusterIP
...

student-node ~ ➜  kubectl get deploy -n kube-system --show-labels | grep coredns
coredns   2/2     2            2           66m   k8s-app=kube-dns



Let's update the kube-dns service it to point to correct set of pods:



student-node ~ ➜  kubectl patch service -n kube-system kube-dns -p '{"spec":{"selector":{"k8s-app": "kube-dns"}}}'
service/kube-dns patched

student-node ~ ➜  kubectl get ep -n kube-system kube-dns 
NAME       ENDPOINTS                                              AGE
kube-dns   10.50.0.2:53,10.50.192.1:53,10.50.0.2:53 + 3 more...   69m



NOTE: We can use any method to update kube-dns service. In our case, we have used kubectl patch command.




Now let's store the correct output to /root/dns-output-12345-cka10-svcn:



student-node ~ ➜  kubectl exec -n ns-12345-svcn -i -t $POD_NAME -- nslookup kubernetes.default
Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   kubernetes.default.svc.cluster.local
Address: 10.96.0.1


student-node ~ ➜  kubectl exec -n ns-12345-svcn -i -t $POD_NAME -- nslookup kubernetes.default > /root/dns-output-12345-cka10-svcn


***SECTION: SERVICE NETWORKING***

For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


Part I:



Create a ClusterIP service .i.e. service-3421-svcn in the spectra-1267 ns which should expose the pods namely pod-23 and pod-21 with port set to 8080 and targetport to 80.



Part II:



Store the pod names and their ip addresses from the spectra-1267 ns at /root/pod_ips_cka05_svcn where the output is sorted by their IP's.

Please ensure the format as shown below:



POD_NAME        IP_ADDR
pod-1           ip-1
pod-3           ip-2
pod-2           ip-3
...

**ANSWER** ->

Switching to cluster3:

kubectl config use-context cluster3

The easiest way to route traffic to a specific pod is by the use of labels and selectors . List the pods along with their labels:



student-node ~ ➜  kubectl get pods --show-labels -n spectra-1267
NAME     READY   STATUS    RESTARTS   AGE     LABELS
pod-12   1/1     Running   0          5m21s   env=dev,mode=standard,type=external
pod-34   1/1     Running   0          5m20s   env=dev,mode=standard,type=internal
pod-43   1/1     Running   0          5m20s   env=prod,mode=exam,type=internal
pod-23   1/1     Running   0          5m21s   env=dev,mode=exam,type=external
pod-32   1/1     Running   0          5m20s   env=prod,mode=standard,type=internal
pod-21   1/1     Running   0          5m20s   env=prod,mode=exam,type=external



Looks like there are a lot of pods created to confuse us. But we are only concerned with the labels of pod-23 and pod-21.


As we can see both the required pods have labels mode=exam,type=external in common. Let's confirm that using kubectl too:



student-node ~ ➜  kubectl get pod -l mode=exam,type=external -n spectra-1267                                    
NAME     READY   STATUS    RESTARTS   AGE
pod-23   1/1     Running   0          9m18s
pod-21   1/1     Running   0          9m17s



Nice!! Now as we have figured out the labels, we can proceed further with the creation of the service:



student-node ~ ➜  kubectl create service clusterip service-3421-svcn -n spectra-1267 --tcp=8080:80 --dry-run=client -o yaml > service-3421-svcn.yaml



Now modify the service definition with selectors as required before applying to k8s cluster:



```student-node ~ ➜  cat service-3421-svcn.yaml 
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: service-3421-svcn
  name: service-3421-svcn
  namespace: spectra-1267
spec:
  ports:
  - name: 8080-80
    port: 8080
    protocol: TCP
    targetPort: 80
  selector:
    app: service-3421-svcn  # delete 
    mode: exam    # add
    type: external  # add
  type: ClusterIP
status:
  loadBalancer: {}
```


Finally let's apply the service definition:



student-node ~ ➜  kubectl apply -f service-3421-svcn.yaml
service/service-3421 created

student-node ~ ➜  k get ep service-3421-svcn -n spectra-1267
NAME           ENDPOINTS                     AGE
service-3421   10.42.0.15:80,10.42.0.17:80   52s



To store all the pod name along with their IP's , we could use imperative command as shown below:



student-node ~ ➜  kubectl get pods -n spectra-1267 -o=custom-columns='POD_NAME:metadata.name,IP_ADDR:status.podIP' --sort-by=.status.podIP

POD_NAME   IP_ADDR
pod-12     10.42.0.18
pod-23     10.42.0.19
pod-34     10.42.0.20
pod-21     10.42.0.21
...

# store the output to /root/pod_ips
student-node ~ ➜  kubectl get pods -n spectra-1267 -o=custom-columns='POD_NAME:metadata.name,IP_ADDR:status.podIP' --sort-by=.status.podIP > /root/pod_ips_cka05_svcn


***SECTION: SERVICE NETWORKING***

For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


Create a deployment named hr-web-app-cka08-svcn using the image kodekloud/webapp-color with 2 replicas.



Expose the hr-web-app-cka08-svcn as service hr-web-app-service-cka08-svcn application on port 30082 on the nodes of the cluster.

The web application listens on port 8080.

**ANSWER** ->

Switch to cluster3 :



kubectl config use-context cluster3



On student-node, use the command: kubectl create deployment hr-web-app-cka08-svcn --image=kodekloud/webapp-color --replicas=2



Now we can run the command: kubectl expose deployment hr-web-app-cka08-svcn --type=NodePort --port=8080 --name=hr-web-app-service-cka08-svcn --dry-run=client -o yaml > hr-web-app-service-cka08-svcn.yaml to generate a service definition file.




Now, in generated service definition file add the nodePort field with the given port number under the ports section and create a service.



***SECTION: SERVICE NETWORKING***

For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


Create a nginx pod called nginx-resolver-cka06-svcn using image nginx, expose it internally with a service called nginx-resolver-service-cka06-svcn.

Test that you are able to look up the service and pod names from within the cluster. Use the image: busybox:1.28 for dns lookup. Record results in /root/CKA/nginx.svc.cka06.svcn and /root/CKA/nginx.pod.cka06.svcn

**ANSWER** ->

Switching to cluster1:



kubectl config use-context cluster1



To create a pod nginx-resolver-cka06-svcn and expose it internally:



student-node ~ ➜ kubectl run nginx-resolver-cka06-svcn --image=nginx 
student-node ~ ➜ kubectl expose pod/nginx-resolver-cka06-svcn --name=nginx-resolver-service-cka06-svcn --port=80 --target-port=80 --type=ClusterIP 



To create a pod test-nslookup. Test that you are able to look up the service and pod names from within the cluster:



student-node ~ ➜  kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup nginx-resolver-service-cka06-svcn
student-node ~ ➜  kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup nginx-resolver-service-cka06-svcn > /root/CKA/nginx.svc.cka06.svcn



Get the IP of the nginx-resolver-cka06-svcn pod and replace the dots(.) with hyphon(-) which will be used below.



student-node ~ ➜  kubectl get pod nginx-resolver-cka06-svcn -o wide
student-node ~ ➜  IP=`kubectl get pod nginx-resolver-cka06-svcn -o wide --no-headers | awk '{print $6}' | tr '.' '-'`
student-node ~ ➜  kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup $IP.default.pod > /root/CKA/nginx.pod.cka06.svcn


***SECTION: SERVICE NETWORKING***

For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


We have an external webserver running on student-node which is exposed at port 9999. We have created a service called external-webserver-cka03-svcn that can connect to our local webserver from within the kubernetes cluster3 but at the moment it is not working as expected.



Fix the issue so that other pods within cluster3 can use external-webserver-cka03-svcn service to access the webserver.

**ANSWER**

Let's check if the webserver is working or not:


student-node ~ ➜  curl student-node:9999
...
<h1>Welcome to nginx!</h1>
...



Now we will check if service is correctly defined:

student-node ~ ➜  kubectl describe svc external-webserver-cka03-svcn 
Name:              external-webserver-cka03-svcn
Namespace:         default
.
.
Endpoints:         <none> # there are no endpoints for the service
...



As we can see there is no endpoints specified for the service, hence we won't be able to get any output. Since we can not destroy any k8s object, let's create the endpoint manually for this service as shown below:


student-node ~ ➜  export IP_ADDR=$(ifconfig eth0 | grep inet | awk '{print $2}')

student-node ~ ➜ kubectl --context cluster3 apply -f - <<EOF
apiVersion: v1
kind: Endpoints
metadata:
  # the name here should match the name of the Service
  name: external-webserver-cka03-svcn
subsets:
  - addresses:
      - ip: $IP_ADDR
    ports:
      - port: 9999
EOF



Finally check if the curl test works now:

student-node ~ ➜  kubectl --context cluster3 run --rm  -i test-curl-pod --image=curlimages/curl --restart=Never -- curl -m 2 external-webserver-cka03-svcn
...
<title>Welcome to nginx!</title>
...