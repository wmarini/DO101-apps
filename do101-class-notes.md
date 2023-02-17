Username 			RHT_OCP4_DEV_USER 		gbwwce
Password			RHT_OCP4_DEV_PASSWORD	3e634d9fb1ef45069d33
API Endpoint	RHT_OCP4_MASTER_API		https://api.na46a.prod.ole.redhat.com:6443
Console Web Application		https://console-openshift-console.apps.na46a.prod.ole.redhat.com
Cluster Id		19394d51-9c1f-40f9-ae07-9a8bdae032a9

```bash
$ sudo cp -v oc /usr/local/bin
$ sudo chmod +x /usr/local/bin/oc
$ oc version --client
```

Criando um projeto e baixando do github o codigo fonte. 

```bash
$ oc login --token=sha256~cjhR6K0CLOh1h1hX8gGpsC0S4DEvXrQhMutD-DXgfHE --server=https://api.na46a.prod.ole.redhat.com:6443
$ oc new-project gbwwce-version
```

On Code create a new branch called `update-app`.  

```bash
$ oc new-app --name version https://github.com/wmarini/DO101-apps#update-app --context-dir version
$ oc status
$ oc get all
```

```
$ oc new-app --name version https://github.com/wmarini/DO101-apps#update-app --context-dir version
--> Found image d7de962 (11 months old) in image stream "openshift/nodejs" under tag "12-ubi8" for "nodejs"

    Node.js 12 
    ---------- 
    Node.js 12 available as container is a base platform for building and running various Node.js 12 applications and frameworks. Node.js is a platform built on Chrome's JavaScript runtime for easily building fast, scalable network applications. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient, perfect for data-intensive real-time applications that run across distributed devices.

    Tags: builder, nodejs, nodejs12

    * The source repository appears to match: nodejs
    * A source build using source code from https://github.com/wmarini/DO101-apps#update-app will be created
      * The resulting image will be pushed to image stream tag "version:latest"
      * Use 'oc start-build' to trigger a new build

--> Creating resources ...
    imagestream.image.openshift.io "version" created
    buildconfig.build.openshift.io "version" created
    deployment.apps "version" created
    service "version" created
--> Success
    Build scheduled, use 'oc logs -f buildconfig/version' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose service/version' 
    Run 'oc status' to view your app.

#-------------------------------    
$ oc status
In project gbwwce-version on server https://api.na46a.prod.ole.redhat.com:6443

svc/version - 172.30.251.206:8080
  deployment/version deploys istag/version:latest <-
    bc/version source builds https://github.com/wmarini/DO101-apps#update-app on openshift/nodejs:12-ubi8 
    deployment #2 running for about a minute - 1 pod
    deployment #1 deployed 2 minutes ago


1 info identified, use 'oc status --suggest' to see details.

#-------------------------------
$ oc get all
NAME                           READY   STATUS      RESTARTS   AGE
pod/version-1-build            0/1     Completed   0          4m1s
pod/version-5c7bb6c96c-ndgcl   1/1     Running     0          3m5s

NAME              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/version   ClusterIP   172.30.251.206   <none>        8080/TCP   4m2s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/version   1/1     1            1           4m3s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/version-5c7bb6c96c   1         1         1       3m6s
replicaset.apps/version-6bd57d5f4f   0         0         0       4m3s

NAME                                     TYPE     FROM             LATEST
buildconfig.build.openshift.io/version   Source   Git@update-app   1

NAME                                 TYPE     FROM          STATUS     STARTED         DURATION
build.build.openshift.io/version-1   Source   Git@ba2af85   Complete   4 minutes ago   56s

NAME                                     IMAGE REPOSITORY                                                                               TAGS     UPDATED
imagestream.image.openshift.io/version   default-route-openshift-image-registry.apps.na46a.prod.ole.redhat.com/gbwwce-version/version   latest   3 minutes ago

```

Adding a *route* 

```bash
$ oc expose svc/version
$ oc get route
```

```
wmarini@seattle:~/projects/DO101-apps$ oc expose svc/version
route.route.openshift.io/version exposed
wmarini@seattle:~/projects/DO101-apps$ oc get route
NAME      HOST/PORT                                               PATH   SERVICES   PORT       TERMINATION   WILDCARD
version   version-gbwwce-version.apps.na46a.prod.ole.redhat.com          version    8080-tcp                 None
```

On Code change `app.js` to version "2".  

Commit and Pull.  

Criar um webhook no github com as definições apresentadas em openshift console ("Builds"->"Webhooks") e coloca no github em  "Settings" (do repositorio) -> "Webhooks". Com isso, qualquer atualização do codigo será reproduzida no OpenShift. Se voce modificar um codigo finte, automaticamente sera modificado o projeto e a mudança sera implementada.



