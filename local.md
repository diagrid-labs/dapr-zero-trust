# Running locally

1. Compile and package the pizza-delivery service

    ```bash
    cd pizza-delivery
    mvn compile
    mvn package
    ```

2. Compile and package the pizza-kitchen service

    ```bash
    cd pizza-kitchen
    mvn compile
    mvn package
    ```

3. Compile and pacakge the pizza-store service

    ```bash
    cd pizza-store
    mvn compile
    mvn package
    ```

4. Start dapr multi app run in the root

```bash
dapr run -f . 
```

```http
http://localhost:8080
```