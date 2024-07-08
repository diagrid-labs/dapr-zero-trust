# Zero Trust Pizza Store

This repository contains a distributed Pizza Store application using Kubernetes, [Dapr](https://dapr.io) and [Spring Boot](https://spring.io/projects/spring-boot) that showcases how Dapr can be used to implement and enforce zero trust development capabilities.

To read about the application architecture and for complete instructions on how to deploy it on Kubernetes, see [Conductor Pizza Store](https://github.com/diagrid-labs/conductor-pizza-store). This repository will just discuss the zero trust layer that is implemented on top of the application.

This repository accompanies the [Diagrid Zero Trust blog series](https://diagrid.ws/zero-trust-blog).

## Set-up Instructions

1. Follow the instructions to deploy the Pizza Store System on Kubernetes as described [here](https://github.com/diagrid-labs/conductor-pizza-store?tab=readme-ov-file#installation).

1. Instead of the Kubernetes manifests located in Conductor Pizza Store `k8s` [folder](https://github.com/diagrid-labs/conductor-pizza-store/tree/main/k8s), apply the manifests located in `k8s` folder.

    ```bash
    kubectl apply -f k8s/
    ```

1. To avoid dealing with Ingresses you can access the Pizza Store application by using kubectl port-forward, run to access the application on port 8080:

    ```bash
    kubectl port-forward svc/pizza-store 8080:80
    ```

1. Similarly, port-forward the Dapr HTTP ports of the pizza, delivery and kitchen pods to access them locally as follows:

    ```bash
    kubectl port-forward pizza-store-deployment-<POD_ID> 3500:3500
    kubectl port-forward pizza-delivery-deployment-<POD_ID> 3501:3500
    kubectl port-forward pizza-kitchen-deployment-<POD_ID> 3502:3500
    ```

1. Stream the application and Dapr sidecar logs of Pizza Store and Kitchen Service:

    ```bash
    kubectl logs -l app=pizza-store-service -f
    kubectl logs -l app=pizza-store-service -f -c daprd

    kubectl logs -l app=pizza-kitchen-service -f
    kubectl logs -l app=pizza-kitchen-service -f -c daprd
    ```

1. Use your browser to navigate to http://localhost:8080 and you should see the Pizza Store UI. Test a few orders in the system., watching their logs accordingly.

1. Use the [VS Code REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) or another HTTP Client to test the zero trust scenarios desribed [here](./test.rest).

Read more about additional Dapr Security features to add to your distributed apps [here](https://docs.dapr.io/concepts/security-concept/).
