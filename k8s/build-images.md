1. Create the docker image for all three projects via:

```bash
mvn spring-boot:build-image
```

2. Tag to use the gcp registry:

```bash
docker tag pizza-delivery:0.0.1-SNAPSHOT us-central1-docker.pkg.dev/prj-common-d-shared-89549/dapr-distributed-pizza-store/pizza-delivery:0.0.1-SNAPSHOT

docker tag pizza-kitchen:0.0.1-SNAPSHOT us-central1-docker.pkg.dev/prj-common-d-shared-89549/dapr-distributed-pizza-store/pizza-kitchen:0.0.1-SNAPSHOT

docker tag pizza-store:0.0.1-SNAPSHOT us-central1-docker.pkg.dev/prj-common-d-shared-89549/dapr-distributed-pizza-store/pizza-store:0.0.1-SNAPSHOT
```

Images names:

us-central1-docker.pkg.dev/prj-common-d-shared-89549/dapr-distributed-pizza-store/pizza-delivery
us-central1-docker.pkg.dev/prj-common-d-shared-89549/dapr-distributed-pizza-store/pizza-kitchen
us-central1-docker.pkg.dev/prj-common-d-shared-89549/dapr-distributed-pizza-store/pizza-store