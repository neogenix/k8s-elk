# Kubernetes ELK - ElasticSearch, Kibana, Logstash, and all the trimmings

This repository currently includes the ElasticSearch, and Kibana configurations. ElasticSearch
is run in 3 forms. The first is the "master" type, which is the master type from the ElasticSearch
documentation. The second type is the "ingest" type, which is the ingest type from the ElasticSearch 
documentation. The ingest nodes include a horizonalPodAutoscaler based on CPU usage, and these nodes
are connected to an internal service for Kibana, as well as an external service for HTTP input from outside.

The third type is the "data" node. These are constructed using the statefulSet, and PersistentVolumeClaims
which will scale accordingly. You can only scale ordinally (+1, -1, to the most recent pod), and all general
ElasticSearch rules apply (if you remove more nodes than you can withstand failures between allowing the cluster
to rebalanace itself, you'll be in trouble).

I've used a "headless" service construct to mimic the function of service discovery, and have placed the "master"
ElasticSearch nodes into the service to be used with zen.discovery inside ElasticSearch.

The remaining container configuration is for Kibana at the moment. These simply specify where ElasticSearch is. 
I've worked hard to use the plain vanilla upstream containers from Elastic.co, so you shouldn't have to import
any custom containers from me :)

The repo will include (in the near future) entrypoints for logs via logstash, and all the logstash 
parts, but for the time being those are being reworked elsewhere.

To use these, simply pull the repo down, make a cluster in GKE, and run the following: 

`kubectl create -f configmap-production.yml,es-master,es-ingest,es-data,service-es.yml,kibana,service-kibana.yml`

To delete what you've created, run the following:

`kubectl delete configmap,service,statefulset,deployment,pvc,hpa -l application=logging`

The things I still intend to add to the repo:
- Configure Curator
- Disable the x-pack monitoring through an environment variable
- Better distribution configurations to make sure we don't have too many eggs in one basket
- Better handling of deletes for the statefulSets
- Better handling of security between pods (kibana and logstash shouldn't be able to talk to data-nodes for example)
- Better examples for handling firewall rules in GKE
- Better examples for handling scale up, and scale down
- Better HTTP(s) ?
- Better handling of security (external vs. internal, firewall rules)
- Add Diagram
