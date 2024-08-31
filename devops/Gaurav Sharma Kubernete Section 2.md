# Gaurav Sharma Kubernete Section 2

### youtube playlist url

https://youtube.com/playlist?list=PL6XT0grm_TfhFKUv_KI_DTVr0TCincl1r&si=dAwUhx1WDmMnTcsU 

## Topics Coverend in this section

1. Kubernetes secrets
2. Tent and Tolerations
3. Node Selector and Node Labels
4. Node Affinity
5. Volumes

## Important Questions and Practicals

1. **What is Kubernete Secret ?**
Secret ek kubernete object hai, jo ki sensitive data carry  hai like a password, a token, or a key. Secret ConfigMaps ke jese hi hote hai, means ye bhi key value ke form me data hold karta hai, difference bas itna hai ki ye data confidential hota hai. Kubernete secret is used during the workflow of Pod like creating, viewing, and editing Pods,isse humara system more secure ban jata hai.
2. **What should be the size of source file for creating kubernete secret ?**
When you create a Kubernetes job, the source files will be uploaded as Kubernetes secrets. However, they have a size limit of 1MB.
3. **What is the short form of kubernete secret object ? What is the command for knowing short form of any kubernete object ?**
There is no short form for kubernete secret object. 
`kubectl api-resources // code for knowing the short form of any kubernet object`
4. **Practical** - Creating the secret
Aim 1 -using various approaches for creation like - command line arguments, passing file and directory, using environment file for creating secret.
Aim 2 - Listing all the secret objects

by using command line arguments - 
`kubectl create secret generic my-secret\
    --from-literal=username=admin\
    --from-literal=password=hello123\`

 by passing the data files for secret
`kubectl create secret generic my-secret\
    --from-file=./application.properties` 

by passing the environment files for secret
`kubectl create secret generic my-secret\
    --from-env-file=./env.sh` 

list the secret
`kubectl get secrets` 
5. **Practical -** create a secret using yaml file
                - encrypt the data using the terminal
                - try to see the encrypted data

```jsx
**apiVersion**: v1
**kind**: Secret
**metadata**:
  **name**: mysecret
**type**: Opaque
**data**:
  **username**: YWRtaW4=   # data encrypted hona chahiye, mumko encrypt karnke dena hoga
  **password**: MWYyZDFlMmU2N2Rm
```

`echo -n “<your-data>” | base64 // to encrypt the data with base64 
 kubectl describe secret <secret-name>  // an effort to see the data of secret 
 kubectl get secrets <secret-name> -o yaml  // hum secret ke baare mei jaan sakte hai
 kubectl describe secret <secret-name>  // this is useless, humko kuch nhi milega`

1. **Practical** - create a yaml file for creating secret object in kubernete without using code editor

Agar humare paas data hai to hum effortlessly secret create kar sakte hai by using yaml file and without using yaml file. 
`kubectl create secret generic <secret-name> - -from-literal=key=value --dry-run="client" -o yaml`

By using above command, hume only yaml code milega, humko use ek file me store karna hai and then correction.
`<above-code>   > secret.yaml
creationTimestamp:null  // delete this line` 
. 
2. **Practical** - Write a yaml file to create a pod such that to inject the secret
Aim - Inject the secret in the pod such that only selected variable comes
Aim - Inject the secret in the pod such that all the variables comes at once
Aim - inject the  secret in the pod such that all the secret come in pod as a file

Creating a yaml file for pod creation such that injecting secret in a pod variable by variable (pod-secret-variable-wise.yml) 

```yaml
# Here we are creating a pod, jisme ek secret jo ki already created hai, vo inject hoga.
# We are injecting the secret variables one by one

apiVersion: v1  # these 2 sections are fixed as per (kubectl explain <resource name>)
kind: Pod
metadata: # data about data
  name: pod-secret-variable-wise
spec:
  containers:  # it’s an array, you will specify all the containers
    - name: containername
      image: nginx #hum ek hi container ki image ko  use karre hai 
      imagePullPolicy: Never     # kubernet first check the image in local machine
      env:
       -  name: database-username       # name of your custom variables
          valueFrom:
            secretKeyRef:
              key: username # variable-name-in-secret
              name: mysecret # your-secret-name

       -  name: database-password       # name of your custom variables
          valueFrom:
            secretKeyRef:
              key: password # variable-name-in-secret
              name: mysecret # your-secret-name

```

`kubectl apply -f pod-secret-variable-wise.yml`

checking our environment variable
`kubectl exec -it pod-secret-variable-wise -- bash // for executing the shell 
 env   // for checking the environment variables`   

Create a yaml file for pod creation and injecting all the variables of secret at once (pod-secret-variable-all.yml)

```yaml
# Here we are creating a pod, jisme ek secret jo ki already created hai, vo inject hoga.
# We are injecting the secret - all  variables at once

apiVersion: v1  # these 2 sections are fixed as per (kubectl explain <resource name>)
kind: Pod
metadata: # data about data
  name: pod-secret-all-variables
spec:
  containers:  # it’s an array, you will specify all the containers
    - name: containername
      image: nginx #hum ek hi container ki image ko  use karre hai 
      imagePullPolicy: Never     # kubernet first check the image in local machine

      envFrom: # we are injecting all the secret at once
       - secretRef:
           name: mysecret # this requires only secret name

```

`kubectl apply -f pod-secret-variable-all.yml`

Create a yaml file for pod creation and injecting the secret as file (pod-secret-variable-file.yml)

```yaml
# Here we are creating a pod, jisme ek secret jo ki already created hai, vo inject hoga.
# We are injecting the secret - as a file
# We are going to mount a voume ( voume type - secret) 

apiVersion: v1  # these 2 sections are fixed as per (kubectl explain <resource name>)
kind: Pod
metadata: # data about data
  name: pod-secret-as-file
spec:
  containers:  # it’s an array, you will specify all the containers
    - name: containername
      image: nginx #hum ek hi container ki image ko  use karre hai 
      imagePullPolicy: Never     # kubernet first check the image in local machine
      
      volumeMounts:               # secret ko as a file lene ke liye, humko ek volume mount karna hoga
       -  mountPath: /secrets-dir  # ye wali directory ke andar secret banega
          name: my-secret-volume  # both the volume names should be same

  volumes:    # we have to create a volume
   -  name: my-secret-volume  # both the volume names should be same
      secret:
        secretName: mysecret # secret object name

```

`kubectl apply -f pod-secret-variable-file.yml`

1. **How to inject some selected variables into the pod as a file**
2. **Explain the concept of Tent and Toleration** 
The concept of Tent and Toleration provides a mechanism to ensure that pods are not scheduled on inappropriate nodes. We can impose the restrictions on to reject which pods are not eligible to be schedule on the particular node, such restrictions are Taints. For making pod eligible to be schedule on a particular node, we set Toleration in the pod. If a  Pod have toleration to a Taint, there is no gaurantee that that Pod will be scheduled to that perticular node. Pod kisi bhi node me schedule hone ke liye swatantra hai.

.
3. **Practical** - Set the Taint to a node and Toleration to a Pod 

Firstly we will check the nodes and check the already attached Taint on a node then we will set a taint to a node  
`kubectl get nodes -o wide  # checking all the attached nodes
kubectl describe nodes <node-name> | less   # checking any existing Taint on a node
kubectl taint node <node-name> mysize=large:NoSchedule  # setting taint to a node`
`kubectl describe nodes <node-name> | less   # checking whether our Tent is applied to a node or not` 

After setting Taint to a node we will set Toleration to a pod (pod-toleration.yml)

```yaml
# Yaha par hum ek pod create karenge jisme tum toleration se karenge
# Node "minikube mei ek Taint set kiya hai by uing the command - kubectl taint node minikube mysize=large:NoSchedule

apiVersion: v1  # these 2 sections are fixed as per (kubectl explain <resource name>)
kind: Pod
metadata: # data about data
  name: pod-secret-as-file
spec:
  containers:  # it’s an array, you will specify all the containers
    - name: containername
      image: nginx #hum ek hi container ki image ko  use karre hai
      imagePullPolicy: Never     # kubernet first check the image in local machine

  tolerations:
    - effect: "NoSchedule" # empty effect means all effects will be valid
      key: "mysize" # empty key means pod can tolerate all Taints
      operator: "Equal" # not giving operator line will make default operator - Equal
      value: "large"  # empty value means all values will be valid

```

`kubctl apply -f pod-toleration.yml`

Checking whether our Taint and Toleration correctly working
`kubectl get pods -o wide    # checking ki humara pod kaha par bana hai
kubectl describe pod pod-toleration      # scroll karke Toleration wali property check karo`

1. **What is effect field in toleration definition in yaml file of pod**
In the context of Kubernetes, the `effect` field in a toleration definition within a pod's YAML file specifies the type of taint that the pod can tolerate. The `effect` field can take one of three possible values, which correspond to the effects specified in taints applied to nodes:

**`NoSchedule`**: This effect means that the pod will scheduled only when toleration will be matched otherwise Pod won’t be scheduled.

**`PreferNoSchedule`**: This effect will try to avoid the scheduling of pod if toleration won’t match but if Pod don’t have other option then Pod can be scheduled even without matching the key and value of toleration.
Kubernetes will try to avoid scheduling the pod on the node with this taint, but it is not guaranteed. If there are no other options, the pod may still be scheduled on the node.

**`NoExecute`**: This effect means that the pod will tolerate a taint that would match otherwise cause to prevent from being scheduled onto the node. If a node has a taint with the `NoExecute` effect, pods that do not have a matching toleration then the Pod will be terminated from the node even if it is already running there.

1. **Refer Hand Notes** - Suppose node Worker01 me ek Taint hai and Pod par corresponding Toleration set kiya hai. Is it 100% fixed ki humara corresponding Pod us Worker01 node mei hi create hoga.
2. **Refer Hand Notes** - What do you meant by NoSchedule, PreferNoSchedule and NoExecute
3. **Practical** - Create a tent to  a node without creating Toleraton to a Pod. Ensure to get Pod status as Pending. Then remove the Taint of a node and see that our Pod is scheduled to node after removal of Taint.

Step1 : Create a Taint to a node as size=Large:NoSchedule
Step2 : Create a Pod without creating Toleration
Step3 : Untaint the node

`kubectl taint node <node-name> size-  # This minus sign show that we are removing the label
kubectl describe node <node-name>  # Here we are checking that Taint is removed or not`

1. **Practical** - What result will come after implementing following things to Toleration definitions - 
- Don’t provide operator line
- Keeping the  effect field as blank
- Setting the operator as “Exist” without giving the value line
- Keeping the key filed as blank

Creating the Taint to a node
`kubectl taint node <node-name> mysize=large:NoSchedule  # setting taint to a node`

Considering the rest of the yaml file for pod creation as same

```yaml
apiVersion: v1  # these 2 sections are fixed as per (kubectl explain <resource name>)
kind: Pod
metadata: # data about data
  name: pod-toleration-experiments
spec:
  containers:  # it’s an array, you will specify all the containers
    - name: containername
      image: nginx #hum ek hi container ki image ko  use karre hai
      imagePullPolicy: Never     # kubernet first check the image in local machine

```

Experimenting with Tolerations

```yaml
# Not provide operator line # file2 done
  tolerations:
    - effect: "NoSchedule" 
      key: "mysize" 

      value: "large" 
      
# Setting the operator as “Exist” without giving the value line # file3
  tolerations:
    - effect: "NoSchedule" 
      key: "mysize" 
      operator: "Exists" 

      
      
# Keeping the key filed as blank # file4
  tolerations:
    - effect: "NoSchedule" 
      key: "" 
      operator: "Exists" 

     
 # Keeping the Effect field empty  # file5n 
  tolerations:
    - effect: ""      # Pod will tolerate all values of Effect
      key: "mysize" 
      operator: "Equal" 
      value: "large" 
      

```

Result of our above experiments 

1. **Not provide operator line**
By Default yaha par equal operator consider kar leta hai
2. **Setting the operator as “Exist” without giving the value line**
Pod toleration only key ke base par hoga but value field ko empty chordhna hoga
error says - value must be empty when operator is 'Exists’

3. **Keeping the key filed as blank**
Pod can tolerate everything. It means whatever will be the key set to Taint of a node, node is elligible for all sort of Taints
Agar hum effect field hata de to fir ye kisi bhi effect ko Tolerate kar sakta hai, means Effect field ke matching nhi hogi.
Agar ye perticular field ki value match nhi karega, iska matlab ye nhi ki tum usme galat value daal no, then ye error generseate karega, so agar kisi field ki value match nhi ho rahi ho to fir usko empty leave kar
4. **Keeping the effect field as blank**
Pod will tolerate as it will not consider the matching the effect field

1. **Describe tolerationSeconds attribute in toleration** 

Creating a pod with the property tolerationSeconds  - pod-toleration-seconds.yml

```yaml
# Yaha par hum toleraton seconds set kerege jisme pod ka ek life-time set ho jata hai

apiVersion: v1  # these 2 sections are fixed as per (kubectl explain <resource name>)
kind: Pod
metadata: # data about data
  name: pod-toleration-experiments
spec:
  containers:  # it’s an array, you will specify all the containers
    - name: containername
      image: nginx #hum ek hi container ki image ko  use karre hai
      imagePullPolicy: Never     # kubernet first check the image in local machine
  tolerations:
       - effect: "NoExecute" 
         key: "mysize" 
         operator: "Equal" 
         value: "large"
         tolerationSeconds: 60   # agar kisi node ke Taint ko humara pod toleration karega 
                                 # to fir ye toleration only 60 seconds ke liye hoga

```

`kubectl apply -f pod-toleration-no-execute.yml`

ek error aati hai, because mene effect NoSchedule set kiya tha. Error ye thi ki - “effect must be 'NoExecute' when tolerationSeconds is set”

After this sir screen ko split karte hai, ek screen me sir pod ko watch karte hai (jese hi node ko NoExecute wala Taint attach hoga, humara pod 60 seconds ke baad terminate ko jayega.

`watch “kubectl get pods -o wide”`

`kubectl taint node worker01 mysize=large:NoExecute` 

**Sir tell something special**  - humare pod me similarly 2 labes attached hote hai i.e. NoExecute wale. Ye labels se pod 600 seconds (5 minutes ) baad delete ho jata hai agar humara node “Not Ready” ya fir “Unreachable” hota hai.
`kubectl describe pod <pod-name>  # try to see at toleration section`

1. **Suppose if a node is having very good configuration and capabale for providing environment for heavy applications then how can we use that fast node ? Describe the condept of node Selector with respect to Toleration concept.**
Here hum fast node ko ek label provide karn denge jisse humare fast node other node ke between easily differentiate ho sakta hai. Jab hum pod create karenge to fir hum Pod me node selector laga denge jisse Pod (ho ki heavy application ko represent karta hai) only large configuration wale node me hi chalega ya fir schedule hoga.

**Node Selector w.r.t Toleration** - In the concept of toleration humara Pod requirement ke according suitable node me schedule hona prefer karta hai. But iski koi gaurantee nhi ki humare Pod usi node me 100% schedule hoga, humar Pod kisi bhi other node me schedule hone ke liye poori tarah se “Swatantra” hai.

When we set the node selector to a Pod then humare pod ussi node me schedule hota hai, jimse corresponding label present hota hai. It means humare pod only suitable node me hi schudule hoga , kisi other pod me nhi, iski poori gaurantee hai.

1. **Practical** - First create a Pod with a node Selector and show that Pod Schedule is unsuccessful. Then attacha a corresponding label to the node and then show that Pod Schedule is successful.

Attaching a node selector into the pod definition  (pod-node-selector.yml)

```yaml
apiVersion: v1  # these 2 sections are fixed as per (kubectl explain <resource name>)
kind: Pod
metadata: # data about data
  name: pod-node-selector
spec:
  containers:  # it’s an array, you will specify all the containers
    - name: containername
      image: nginx #hum ek hi container ki image ko  use karre hai
      imagePullPolicy: Never
  nodeSelector:
    size: "large"  # This pod will be created only in the node with label size=large
                   # Otherwise it will show that pod will be in pending state
```

`kubectl apply -f pod-node-selector.yml
 kubectl get pods -o wide # watching the status of our pod, whether it is created or not`

Attatching a label to a node
`kubectl label node <node-name> size=large
 kubectl get nodes --show-labels  # see the attached labels
 kubectl label node <node-name> size-  # by this dash " - " we can remove the label from nodeka
 kubectl label --overwirite node <node-name> size=small` 

1. **Refer Hand Notes** - What will happen agar hum esa Pod create kare jiska node selector kisi bhi node ke label se match na kare.
1. **Practical** - Implement preferredduringschedulingignoredduringexecution and requiredDuringSchedulingIgnoredDuringExecution

Firstly we will assign the label to the node
`kubectl label node <node-name> size=small`

preferredDuringSchedulingIgnoredDuringExecution :
We are creating pod pod-node-affinity-soft.yml

```yaml
# Hum ek Pod create karre hai, jo ki nodeSelector ke according Scheduleing karega, are matching nhi hogi to fir bhi ye kha bhi pod ko schedule kar dega
# implementation of preferredDuringSchedulingIgnoredDuringExecution => hum preference de rahi hai, but koi compulsary nhi hai

apiVersion: v1
kind: Pod
metadata:
  name: pod-node-affinity-soft
spec:
  containers:
  - name: with-node-affinity
    image: nginx
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: size
            operator: In
            values:
            - small # at next try give size = extralarge, you will see your pod would be still created.

```

`kubectl apply -f pod-node-affinity-soft.yml  # always to do do the dry run everytime --dry-run="client"`
`kubectl get pods -o wide  # always check your pod after creation` 

Next time tum Node selector me size = extralarge de do and you will see that pod will still get scheduled successfully 

requiredDuringSchedulingIgnoredDuringExecution: 
We are creating pod pod-node-affinity-hard.yml . This time kisi bhi node me size=extralarge wala label presnt nhi hai. Remove the label if you want to remove 
`kubectl label node <node-name> size- # removing the label from node`

```yaml
# Hum ek Pod create karre hai, jo ki nodeAffinity (like node selector)  ke according Scheduleing karega, agar matching nhi hogi to fir bhi ye kha bhi pod ko schedule kar dega
# implementation of requiredDuringSchedulingIgnoredDuringExecution => hum requirement de rahi hai, thus compulsary  hai

apiVersion: v1
kind: Pod
metadata:
  name: pod-node-affinity-hard
spec:
  containers:
  - name: with-node-affinity
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: size
            operator: In
            values:
            - extralarge
```

`kubectl apply -f pod-node-affinity-hard.yml`

`kubectl get pods -o wide`

it will show the following error  as failedScheduling

`0/1 nodes are available: 1 node(s) didn't match Pod's node affinity/selector. preemption: 0/1 nodes are available`

you will observe that pod created nhi hai because label ka matching required hai. Now jab tum kisi node ko label provide kar doge to fir tumhara pod successfully schedule ho jayega.

`kubectl label node <node-name> size=extralarge`

1. **What is the use of " weight : 1" in the preferredDuringSchedulingIgnoredDuringExecution.**
If you have multiple nodes with different sizes labeled, this affinity rule prefers nodes labeled with `size: small`. Since the weight is `1`, if there are other affinity rules with higher weights, those rules will have a higher priority. If there are no other rules or all rules have the same weight, the scheduler will prefer nodes labeled `size: small`.

iska matlab ye hai ki pod ke liye multiple affinity rule at the same time supported hai. Agar hume kisi rule ko more priority dena hai to hum “weight” field ki value badha kar ye configure kar sakte hai.

1. **Refer Hand Notes** - What is node Affinity
- description of preferredDuringSchedulingIgnoredDuringExecution and requiredDuringSchedulingIgnoredDuringExecution
- which have more priority nodeSelector or nodeAffinity
- removing label of a node containing runnig pod of marching ( label matching with node Affinity) 

1. **What is the need of volumes in kuberntes**

1. Data Persistence

By default, the filesystem within a container is ephemeral. When a container is terminated and restarted, any data stored within it is lost. Volumes provide a way to persist data beyond the lifecycle of a single container.

2. Data Sharing Between Containers

In Kubernetes, multiple containers can run within a single pod. Volumes allow these containers to share data with each other. This is useful for scenarios where one container generates data that another container needs to process.

3. Access to External Storage

Volumes enable pods to access external storage systems, such as network file systems (NFS), cloud storage services (like AWS EBS, Google Persistent Disk), and databases. This allows for greater flexibility and the use of existing storage infrastructure.

4. Separation of Data from Container Lifecycle

Volumes decouple data storage from the lifecycle of individual containers. This ensures that even if a container fails and is restarted, the data it was using remains intact and accessible.

5. Configurations and Secrets Management

Volumes can be used to manage configurations and secrets. Kubernetes provides special volume types, such as ConfigMap and Secret, which can be used to pass configuration data and sensitive information (like passwords and API keys) to containers securely.

6. Backups and Data Recovery

Volumes make it easier to implement backup and data recovery strategies. By using persistent volumes, administrators can ensure that critical data is backed up and can be restored in the event of a failure.

1. **Practical -** What are the Common Types of Volumes in Kubernetes, Create Pods to describe each type
- **emptyDir**: Pod ka volume

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-emptydir
spec:
  containers:
    - name: mycontainer
      image: nginx
      volumeMounts:
        - mountPath: /data
          name: emptydir-volume
  volumes:
    - name: emptydir-volume
      emptyDir: {}

```

- **hostPath**: Node ka volume

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-hostpath
spec:
  containers:
    - name: mycontainer
      image: nginx
      volumeMounts:
        - mountPath: /my-data
          name: hostpath-volume
  volumes:
    - name: hostpath-volume
      hostPath:
        path: /path/on/host
        type: Directory

```

- **persistentVolumeClaim (PVC)**: A way for users to request and consume storage resources, which are backed by Persistent Volumes (PVs).

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-pvc
spec:
  containers:
    - name: mycontainer
      image: nginx
      volumeMounts:
        - mountPath: /data
          name: pvc-volume
  volumes:
    - name: pvc-volume
      persistentVolumeClaim:
        claimName: my-pvc

```

- **configMap**: Provides configuration data to pods.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-configmap
spec:
  containers:
    - name: mycontainer
      image: nginx
      volumeMounts:
        - mountPath: /config
          name: config-volume
  volumes:
    - name: config-volume
      configMap:
        name: my-configmap

```

- **secret**: Provides sensitive data to pods, such as passwords and keys.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-secret
spec:
  containers:
    - name: mycontainer
      image: nginx
      volumeMounts:
        - mountPath: /secrets
          name: secret-volume
  volumes:
    - name: secret-volume
      secret:
        secretName: my-secret

```

- **nfs**: Mounts an NFS (Network File System) share.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-nfs
spec:
  containers:
    - name: mycontainer
      image: nginx
      volumeMounts:
        - mountPath: /nfs
          name: nfs-volume
  volumes:
    - name: nfs-volume
      nfs:
        server: nfs-server.example.com
        path: /path/on/nfs

```

- **awsElasticBlockStore**: Mounts an AWS EBS volume.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-aws-ebs
spec:
  containers:
    - name: mycontainer
      image: nginx
      volumeMounts:
        - mountPath: /ebs
          name: ebs-volume
  volumes:
    - name: ebs-volume
      awsElasticBlockStore:
        volumeID: vol-0a89b8a1234567890
        fsType: ext4

```

- **gcePersistentDisk**: Mounts a Google Compute Engine persistent disk.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-gce-pd
spec:
  containers:
    - name: mycontainer
      image: nginx
      volumeMounts:
        - mountPath: /gce
          name: gce-pd-volume
  volumes:
    - name: gce-pd-volume
      gcePersistentDisk:
        pdName: my-gce-disk
        fsType: ext4

```

1. **Refer Hand Notes** - Description of emptyDir and hostPath Volume
- Executing a shell, running in a container
- How to restart a container
- How to take ssh to minikube

1. **Describe the uses of grep command**
    1. grep “<expression>” file.txt  # comman syntax , quotation is not important
    2. grep -i “<expression>” file.txt  # ignoring the case
    3. grep -iv “<expression>” file.txt  # ignoring the expression matching line
    4. grep -c “<expression>” file.txt # find the occourance of an expression, return the number of line 
    5. grep -w “<exact-word>” file.txt  # get the line containt exact word
    6. grep -n “<expression>” file.txt  # tet the expression line along with line number
    7. grep “<expression>” file1.txt file2.txt file3.txt  #  search the expression in multiple files, get output along with file name
    8. grep  -h “<expression>” file1.txt file2.txt file3.txt  # get only content , not get file name
    9. grep -e “expression1” -e  “expression2” …. file.txt  # search miltiple expression in a file
    10. egrep “exp1|exp2|exp3” file  # same as above , bas thoda short way hai, -e baar baar  use nhi karna
    11. grep -l “expression” file1.txt file2.txt file3.txt   # get only file name, not the content
    12. grep -f keywords.txt file # using file instead of expression, list of expression in the keywords.txt
    13. grep “^keyword” file  # get the lines that are starting with the provided keyword
    14. grep “keyword$” file  # get the lines that are ending with the provided keyword
    15. grep -R “keyword” dirA/  # search the keyword within a directory
    16. grep -q “keyword” file # search but don’t get output 
    17. echo $?  # above command ke baad chalanna hai, exit status , 0→ command executed, 1→ some problem occoured
    18. grep -s “keyword” file # search but don’t get error message
    19. ls | grep “keyword” # filter the output of a terminal command
    20. 
    21. 
    22. 

26 

**34.Tell some insturction about vim ediotor**

shift + G => for going at bottom line

gg => for going to top line

/<word>  n => searching word in forward direction

?<word>  n => searching word in backword direction

* => search the current cursor word in forward direction

#               => search the current cursor word in backward direction

:%s/<old-word>/<new-word>/g     => replacing all the words

u   => for undiong changes

ctl + r => for redoing changes

o => start writing from next line of cursor

shift + o => start writing from previous line of cursor

shift + i  => start writing from starting of cursor line

shift + a => start writing from ending of cursor line

d => deleting the text by special selection

:e!   => undo to original file

p => paste

y => copy

shift + v => select whole line

:set nu => setting the line  number

:set nonu => removing the line number

1. **Create a PVC in kubernete along with provisioning of EBS Volume with Storage Classes

Corresponding video** - [https://youtu.be/NO_RYhw5v8E](https://youtu.be/NO_RYhw5v8E)

****Step 0: Create a kubernete cluster in AWS EC2
it is easy
We use the aws EKS Service

****Step 1: Create a role in AWS and Provide it EC2 full access

For creating the role in AWS, first create an IAM User

Username : k8s-user
provide him EC2 full access

Step 2: Create access key and Secret Key

For creating access key and secret key
command line interface

Step 3: Create a generic secret in kubernete for accessing to AWS

`kubectl create secret generic aws-secret \
--namespace kube-system \
--from-literal "key_id=" \
--from-literal "access_key="`

Step 4: Install the Helm to Kubernete Cluster and do the remaining process

Run the following command to install heml

`curl -fsSL -o get_helm.sh [https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3](https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3)
chmod 700 get_helm.sh
./get_helm.sh`

Add the AWS EBS CSI Driver Helm chart repository:

`helm repo add aws-ebs-csi-driver [https://kubernetes-sigs.github.io/aws-ebs-csi-driver](https://kubernetes-sigs.github.io/aws-ebs-csi-driver)
helm repo update`

Install the driver
`helm upgrade --install aws-ebs-csi-driver \
--namespace kube-system \
aws-ebs-csi-driver/aws-ebs-csi-driver`

Verify that the driver has been deployed and the pods are running:

`kubectl get pods -n kube-system -l [app.kubernetes.io/name=aws-ebs-csi-driver](http://app.kubernetes.io/name=aws-ebs-csi-driver)`

Step 5: Check the EBS CSI Driver (pods) are running properly or not, this shows our csi driver is deployed properly
Step 6: Create the storage class object

`apiVersion: [storage.k8s.io/v1](http://storage.k8s.io/v1)
kind: StorageClass
metadata:
name: ebs-sc
provisioner: [ebs.csi.aws.com](http://ebs.csi.aws.com/)
parameters:
type: gp2
volumeBindingMode: WaitForFirstConsumer`

kubectl apply -f storageclass.yml

Step 7: Create the PVC Object 

`apiVersion: v1
kind: PersistentVolumeClaim
metadata:
name: ebs-claim
spec:
accessModes:
- ReadWriteOnce
storageClassName: ebs-sc
resources:
requests:
storage: 1Gi`

kubectl apply -f pvc.yml

Step 8: Create a pod in which we are creating persistance volume and mount it to pod

```yaml
apiVersion: v1
kind: Pod
metadata:
name: app
spec:
containers:

- name: app
image: centos
command: ["/bin/sh"]
args: ["-c", "while true; do echo $(date -u) >> /data/out.txt; sleep 5; done"]
volumeMounts:
    - name: persistent-storage
    mountPath: /data
    volumes:
- name: persistent-storage
persistentVolumeClaim:
claimName: ebs-claim
```

kubectl apply -f pod.yml

    After this step you will see your new volume is automatically created in AWS
    You will see that your pvc had been made successfully (Status: Pending → Running) show karra
Step 9: Exec the containder and check the mounted volume (pvv volume)

kubectl exec -it app - - bash

         Cat your out.txt file, this shows success of our lab practice
         Check the mounted devices - # lsblk

1. **Creating Kubernete Cluster in AWS**

Video url : [https://www.youtube.com/watch?v=hWzJTQ5UhwY](https://www.youtube.com/watch?v=hWzJTQ5UhwY)
****some link : https://github.com/CloudStrategyOfficial/document-aws-eks-k8s
Note:   Your are working in this region … N vergin 

****

![Untitled](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/Untitled.png)

**Step 1: Create One Defualt VPC for Bastion Host**

it is very easy to create a defalut vpc

rename the resources created - security group, subnets, route tables, nat gateway 

**Step2: Create Bastion Host**

create an environment in cloud9  

**Step 3: Configure AWS Credentials for AWS Authentication**

configuring the bashion host 

creating IAM user with user with only Programmatic Access

(get the access keys of user and use the command - aws configure)

**Step 4: Create Required IAM Execution Role**

we create 2 roles..

role for eks cluster

role for aws managed nodes

**Step 5: Create VPC for EKS Cluster**

name: eks

cidr: 10.0.0.0/16

no of public subnets: 2

no of private subnets: 2

1 nat gateway in 1 az

creating 2 sg - (leave inbound rule and outbound rule untouched )

eks-cluster-sg

eks-worker-sg

**Step 6: Create EKS Cluster**

creating an eks cluster

kubernete 1.23

role: eks-cluster

vpc: eks-vpc

sg: eks-cluster-sg

**Step 7: Install Kubectl Tool**

very easy steps

lscpu | grep Architwecutre  # this tell you the achitecture of linux machine

**Step 8: Add the K8s cluster context in ~/.kube/config file**

establish commounicaton from bashion host to eks cluster is comples, refer video

**Step 9: Create Node Group for your Workload**

it is very easy step

set node group in private subnet

then do some work in kubernetee

**Cleanup**

deleting resourcs are very important

Yrr, mese ye nhi hora tha. Actually mere jo kubectl controller eks API se commounication hi nhi kar pa raha tha
showing error : couldn't get current server API group list: the server has asked for the client to provide credentials

1. **Describe Cloud9 Service in AWS** 

aws cloud9 faq - https://aws.amazon.com/cloud9/faqs/

1. Create a Kubernete Cluster in AWS without using the EKS Service

Documentation used - [https://v1-29.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/](https://v1-29.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

video used - [https://www.youtube.com/watch?v=Cz7hSJNq2GU](https://www.youtube.com/watch?v=Cz7hSJNq2GU)

Step 1: Create 3 EC2 instances master, node1 and node2
Step 2: Configure Master node
install the docker in all the machines
    yum install docker -y
start docker service in all the machines
    systemctl start docker
install kubeadmin
    installing kubernete repository  in all machines

# Set SELinux in permissive mode (effectively disabling it)
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

# creating kubernete repository
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF

# Install kubelet, kubeadm and kubectl:
sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

initialise the kubeadmin to master node

kubeadm init

create the user and give right permissions in all  machines
join the nodes using the link

Step 3: Creating a network
download calico for kubernetes (different steps for node and master)

i faced error 

![Untitled](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/Untitled%201.png)

some more text 

swapoff -a
dnf install -y iproute-tc

- ---------master node --------
yum install docker
hostnamectl set-hostname master
systemctl enable docker
systemctl restart docker
systemctl status docker
free -mh
sestatus

(paste the link) get the kubernete repo link
(paste the link) install the kubernete

systemctl enable kubelet
systemctl restart kubelet
kubeadm init

create the user
give permissions
paste given link
kubectl get nodes

............calico
(paste the calico links)
kubectl apply -f calico.yml

- --------------------worker---------------
hostnamectl set-hostname worker
yum install docker -y
systemctl enable docker
systemctl restart docker

(paste the repo link)
systemctl enable docker
systemctl enable kubelet
systemctl restart docker kubelet
(paste the joining link for worker node)

# Set SELinux in permissive mode (effectively disabling it)

sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

## Hand Notes

![1719538896549193135309.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/1719538896549193135309.jpg)

free space

![1719539058316471345345.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/1719539058316471345345.jpg)

![1719539114796568407844.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/1719539114796568407844.jpg)

![1719539156190836405331.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/1719539156190836405331.jpg)

![1719539181698835777967.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/1719539181698835777967.jpg)

![17195392504081572672113.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/17195392504081572672113.jpg)

![17195392783021262765944.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/17195392783021262765944.jpg)

![17195393084941357868207.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/17195393084941357868207.jpg)

![17195393531021936068697.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/17195393531021936068697.jpg)

![171953938359141329575.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/171953938359141329575.jpg)

![17195394584712046141623.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/17195394584712046141623.jpg)

![1719539495274161015405.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/1719539495274161015405.jpg)

![17195395351141555896179.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/17195395351141555896179.jpg)

![1719539563778386339140.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/1719539563778386339140.jpg)

![1719539590007671403468.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/1719539590007671403468.jpg)

![17195396250191275279474.jpg](Gaurav%20Sharma%20Kubernete%20Section%202%202950dea004444e71a08a28d8427bf2ff/17195396250191275279474.jpg)

To ignore the upper and lower case while searching using grep command in Linux
To search everything except given pattern/keyword using grep command in Linux
To print how many times (count) given keyword present in file using grep command in Linux
To search for exact match of given keyword in a file using grep command in Linux
To print the line no. of matches of given keyword in a file using grep command in Linux
To search a given keyword in multiple files using grep command in Linux
To suppress file names while search a given keyword in multiple files using grep command in Linux
To search multiple keywords in a file using grep command in Linux
To search multiple keywords in multiple file using grep command in Linux
To only print file names which matches given keywords using grep command in Linux
To get the keywords/pattern from a file and match with a another file using grep command in Linux
To print the matching line which start with given keyword using grep command in Linux
To print the matching line which end with given keyword using grep command in Linux
Suppose we have 100 files in a directory (dirA) and we need to search a keyword in all the files using grep command in Linux
We can use egrep command for the multiple keywords search using grep command in Linux
If you just wanna search but don't want to print on terminal or If you want to suppress error message using grep command in Linux