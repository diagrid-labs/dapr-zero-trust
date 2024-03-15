# Cloud-Native Pizza Store

This repository contains a simple example for a Pizza Store application using Kubernetes, [Dapr](https://dapr.io), [Spring Boot](https://spring.io/projects/spring-boot) and [Testcontainers](https://testcontainers.com) to enable developers with an awesome developer experience. [You can find a Quarkus implementation of this application here (Thanks to @mcruzdev1!)](https://github.com/mcruzdev/pizza-quarkus)

You can run this application on any Kubernetes cluster by following the step-by-step insturctions described in this document. You can also start each service using just Maven.

![Pizza Store](imgs/pizza-store.png)

The Pizza Store application simulates placing a Pizza Order that is going to be processed by different services. The application is composed by the Pizza Store Service which serve as the front end and backend to place the order. The order is sent to the Kitchen Service for preparation and once the order is ready to be delivered the Delivery Service takes the order to your door. 

![Architecture](imgs/distr-pizza-store-architecture-v1.png)

As any other application, these services will need to store and read data from a persistent store such as a Database and exchange messages if a more event-driven approach is needed. 

This application uses Redis and Kafka, as they are well-known components among developers. 

![Architecture with Infra](imgs/distr-pizza-store-architecture-clients-v1.png)

As you can see in the diagram, if we want to connect to  Redis from the Pizza Store Service we need to add to our applications the Redis client that must match with the Redis instance version that we have available. A Kafka client is required in all the services that are interested in publishing or consuming messages/events. Because you have Drivers and Clients that are sensitive to the available versions on the infrastructure components, the lifecycle of the application is now bound to the lifecycle of these components. 

Adding Dapr to the picture not only breaks these dependencies, but also remove responsibilities from developers of choosing the right Driver/Client and how these need to be configured for the application to work correctly. Dapr provides developers building block APIs such as the StateStore and PubSub API that developer can use without know the details of which infrastructure is going to be connected under the covers. 

![Architecture with Dapr](imgs/distr-pizza-store-architecture-dapr-v1.png)

When using Dapr, developers can trust that the [building block APIs](https://docs.dapr.io/concepts/building-blocks-concept/) are stable, while the teams in charge of the infrastructure can swap versions and services without impacting the application code or behavior. 

## Prerequisites

1. For this demo you need a Diagrid Conductor account. Sign up for a free account at [diagrid.io/conductor](https://www.diagrid.io/conductor).
2. To run a kind cluster locally you need [Docker Desktop]() and [kind](https://kind.sigs.k8s.io/).

The demo comes with a front-end to place orders manually and see their progress. However, orders can also be placed direcly to the back-end with a REST client, such as the [VS Code REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client). To execute multiple order requests [Ddosify](https://github.com/ddosify/ddosify) can be used. 

> The easiest way to run the demo is to use the devcontainer and open it in VSCode (requires Docker Desktop) or in a GitHub Codespace. The devcontainer has the following preinstalled:
>  - kind & helm
>  - Ddosify
>  - VSCode REST client extension
>  - CodeTour extension

## Installation

If you don't have a Kubernetes Cluster you can [install KinD](https://kind.sigs.k8s.io/docs/user/quick-start/) to create a local cluster to run the application. 

1. Once you have KinD installed you can run the following command to create a local Cluster: 

```bash
kind create cluster
```

2. Install metrics server (required for Conductor):

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

kubectl patch deployment metrics-server -n kube-system --type "json" -p '[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value": "--kubelet-insecure-tls"}]'
```

3. Install the Conductor Agent (get the manifest link from the [Conductor dashboard -> Create Cluster](https://conductor.diagrid.io/clusters/create). Ensure that Dapr installation is checked.

```bash
kubectl apply -f "https://api.diagrid.io/apis/diagrid.io/v1beta1/clusters\<CLUSTER-ID\>manifests?token=\<TOKEN\>"
```

## Installing infrastructure for the application

1. Redis is used as the key/value store that is used by the Dapr State Managemenr API. Install Redis via helm:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install redis bitnami/redis --set image.tag=6.2
```

2. Kafka is used as the message broker for async communication between services. Install Kafka via helm:

```bash
helm install kafka oci://registry-1.docker.io/bitnamicharts/kafka --version 22.1.5 --set "provisioning.topics[0].name=events-topic" --set "provisioning.topics[0].partitions=1" --set "persistence.size=1Gi"
```

## Installing the Application

To install the application you only need to run the following command: 

```bash
kubectl apply -f k8s/
```

This install all the application services. To avoid dealing with Ingresses you can access the application by using `kubectl port-forward`, run to access the application on port `8080`:

```bash
kubectl port-forward svc/pizza-store 8080:80
```

Then you can point your browser to [`http://localhost:8080`](http://localhost:8080) and you should see:

![Pizza Store](imgs/pizza-store.png)

## Building from source / changing the services

The application services are written using Java + Spring Boot. These services use the Dapr Java SDK to interact with the Dapr [PubSub](https://docs.dapr.io/getting-started/quickstarts/pubsub-quickstart/) and [Statestore](https://docs.dapr.io/getting-started/quickstarts/statemanagement-quickstart/) APIs. 

To run the services locally you can use the [Testcontainer](https://testcontainaers.com) integration already included in the projects. 

For example you can start a local version of the `pizza-store` service by running the following command inside the `pizza-store/` directory (this requires having Java and [Maven](https://maven.apache.org/) installed locally):

```
mvn spring-boot:test-run
```

This, not only start the `pizza-store` service, but it also uses the [Testcontainers + Dapr Spring Boot](https://central.sonatype.com/artifact/io.diagrid.dapr/dapr-spring-boot-starter) integration to configure and wire up a Dapr configuration for local development. In other words, you can now use Dapr outside of Kubernetes, for writing your service tests without the need to know how Dapr is configured. 


Once the service is up, you can place orders and simulate other events coming from the Kitchen and Delivery services by sending HTTP requests to the `/events` endpoint. 

Using [`httpie`](https://httpie.io/) this look like this: 

```
http :8080/events Content-Type:application/cloudevents+json < pizza-store/event-in-prep.json
```

In the Application you should see the event recieved that the order moving forward. 


## Resources and references

- [Cloud native local development with Dapr and Testcontainers](https://www.diagrid.io/blog/cloud-native-local-development)


## More information

Feel free to create issues or get in touch with us using Issues or via [Twitter @Salaboy](https://twitter.com/salaboy)
