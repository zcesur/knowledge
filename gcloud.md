# Concepts

## Cluster Architecture

* [Cluster Architecture | Kubernetes Engine | Google Cloud](https://cloud.google.com/kubernetes-engine/docs/concepts/cluster-architecture)
* [Kubernetes Comic | Kubernetes Engine | Google Cloud](https://cloud.google.com/kubernetes-engine/kubernetes-comic/)

> Google Kubernetes Engine provides a managed environment for deploying, managing, and scaling your containerized applications using Google infrastructure. The environment GKE provides consists of multiple machines (specifically, Google Compute Engine instances) grouped together to form a *cluster*.
>
> In Google Kubernetes Engine, a **cluster** consists of at least one *cluster master* and multiple worker machines called *nodes*. These master and node machines run the Kubernetes cluster orchestration system.
>
> The **cluster master** runs the Kubernetes control plane processes, including the Kubernetes API server, scheduler, and core resource controllers.
>
> A cluster typically has one or more **nodes**, which are the worker machines that run your containerized applications and other workloads. The individual machines are Compute Engine VM instances that GKE creates on your behalf when you create a cluster. A node runs the services necessary to support the Docker containers that make up your cluster's workloads. These include the Docker runtime and the Kubernetes node agent (kubelet) which communicates with the master and is responsible for starting and running Docker containers scheduled on that node.
>
> A **node pool** is a subset of node instances within a cluster that all have the same configuration. When you create a container cluster, the number and type of nodes that you specify becomes the default node pool. Then, you can add additional custom node pools of different sizes and types to your cluster. All nodes in any given node pool are identical to one another.

## Load Balancing

* [Overview of Load Balancing | Load Balancing | Google Cloud](https://cloud.google.com/load-balancing/docs/load-balancing-overview)
* [Setting up HTTP Load Balancing with Ingress | Kubernetes Engine Tutorials | Google Cloud](https://cloud.google.com/kubernetes-engine/docs/tutorials/http-balancer)

> GKE offers integrated support for two types of cloud load balancing for a publicly accessible application:
> 
> 1. You can create **TCP/UDP load balancers** by specifying `type: LoadBalancer` on a [Service](https://kubernetes.io/docs/concepts/services-networking/service/) resource manifest. Although a TCP load balancer works for HTTP web servers, they are not designed to terminate HTTP(S) traffic as they are not aware of individual HTTP(S) requests. GKE does not configure any health checks for TCP/UDP load balancers. See the Guestbook tutorial for an example of this type of load balancer.
> 
> 2. You can create **HTTP(S) load balancers** by using an [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) resource. HTTP(S) load balancers are designed to terminate HTTP(S) requests and can make better context-aware load balancing decisions. They offer features like customizable URL maps and TLS termination. GKE automatically configures health checks for HTTP(S) load balancers.

## Consistency

* [Balancing Strong and Eventual Consistency with Google Cloud Datastore | Cloud Datastore Documentation | Google Cloud](https://cloud.google.com/datastore/docs/articles/balancing-strong-and-eventual-consistency-with-google-cloud-datastore/)

> **Eventual consistency** is a theoretical guarantee that, provided no new updates to an entity are made, all reads of the entity will eventually return the last updated value. The Internet Domain Name System (DNS) is a well-known example of a system with an eventual consistency model. DNS servers do not necessarily reflect the latest values but, rather, the values are cached and replicated across many directories over the Internet. It takes a certain amount of time to replicate modified values to all DNS clients and servers. However, the DNS system is a very successful system that has become one of the foundations of the Internet. It is highly available and has proven to be extremely scalable, enabling name lookups to over a hundred million devices across the entire Internet.
>
> In contrast, traditional relational databases have been designed based on the concept of **strong consistency**, also called immediate consistency. This means that data viewed immediately after an update will be consistent for all observers of the entity. This characteristic has been a fundamental assumption for many developers who use relational databases. However, to have strong consistency, developers must compromise on the scalability and performance of their application. Simply put, data has to be locked during the period of update or replication process to ensure that no other processes are updating the same data.

# How-to Guides

## Configuration

```
gcloud config set project $PROJECT_ID
gcloud config set compute/zone $ZONE
gcloud config set container/cluster $CLUSTER
```

## Compute Engine

```
gcloud compute instances list
```

## Kubernetes Engine

* List existing clusters/node-pools/nodes
```
gcloud container clusters list
gcloud container node-pools list
kubectl get nodes
```

* Update cluster settings
```
gcloud container clusters update cluster-1 \
    --enable-autoscaling \
    --min-nodes 1 \
    --max-nodes 10 \
    --node-pool default-pool

gcloud container clusters resize cluster-1 \
    --size 3 \
    --node-pool default-pool
```

* Get number of available images in each node
```
for node in $(kubectl get nodes | awk '/gke-cluster/ {print $1}'); do
    kubectl get node $node -o json | grep --count "sizeBytes"
done
```

## App Engine

* [Accessing App Engine with Remote API | App Engine standard environment for Python | Google Cloud](https://cloud.google.com/appengine/docs/standard/python/tools/remoteapi)

* Backup data from App Engine
```
/usr/local/google-cloud-sdk/platform/google_appengine/appcfg.py download_data \
    -A s~$PROJECT_ID \
    --url=http://$PROJECT_ID.appspot.com/_ah/remote_api/ \
    --filename=data.csv
```

## Datastore

### Modeling Hierarchical Data
> 1. If you need to do transactional updates on an entire tree, and you're not going to have more than about 1QPS of sustained updates to any one tree, you can use the built in support for heirarchial storage. When creating an entity, you can pass the "parent" attribute to specify a parent entity or key, and when querying, you can use the .ancestor() method (or 'ANCESTOR IS' in GQL to retrieve all descendants of a given entity.
> 
> 2. If you don't need transactional updates, you can replicate the functionality of entity groups without the contention issues (and transaction safety): Add a db.ListProperty(db.Key) to your model called 'ancestors', and populate it with the list of ancestors of the object you're inserting. Then you can easily retrieve everything that's descended from a given ancestor with MyModel.all().filter('ancestors =', parent_key).
> 
> 3. If you don't need transactions, and you only care about retrieving the direct children of an entity (not all descendants), use the approach outlined above, but instead of a ListProperty just use a ReferenceProperty to the parent entity. This is known as an Adjacency List.

* [Storing hierarchical data in Google App Engine Datastore? - Stack Overflow](https://stackoverflow.com/questions/1011814/storing-hierarchical-data-in-google-app-engine-datastore)
* [Modeling Hierarchical Data - Google Groups](http://groups.google.co.jp/group/google-appengine/browse_thread/thread/879cfff68bf9ab3f/)
* [Re: storing and rendering comment threads - Google Groups](https://groups.google.com/forum/#!topic/google-appengine/Vm1Rv4B64wE)

## Storage

* [Creating Instances | Cloud Firestore Documentation | Google Cloud](https://cloud.google.com/filestore/docs/creating-instances)
* [Mounting Fileshares on Compute Engine Clients | Cloud Firestore Documentation | Google Cloud](https://cloud.google.com/filestore/docs/mounting-fileshares)
* [Accessing Fileshares from Kubernetes Engine Clusters | Cloud Firestore Documentation | Google Cloud](https://cloud.google.com/filestore/docs/accessing-fileshares)
* [Add information about using NFS with z2jh · Issue #421 · jupyterhub/zero-to-jupyterhub-k8s](https://github.com/jupyterhub/zero-to-jupyterhub-k8s/issues/421)
* [use NFS for cloud home directories · Issue #401 · pangeo-data/pangeo](https://github.com/pangeo-data/pangeo/issues/401)
* [Setting up EFS storage on AWS — Zero to JupyterHub with Kubernetes 0.7.0 documentation](https://zero-to-jupyterhub.readthedocs.io/en/stable/amazon/efs_storage.html)

## Networking

* [Configuring Domain Names with Static IP Addresses | Kubernetes Engine Tutorials | Google Cloud](https://cloud.google.com/kubernetes-engine/docs/tutorials/configuring-domain-name-static-ip)

## Cloud Build

```
gcloud builds submit --tag gcr.io/$PROJECT_ID/$IMAGE_NAME:$TAG path/to/dir
```
