# bookstore-service-app
Bookstore service app to connect to the bookstore service broker on Cloud Foundry

## Forked

Forked from  
https://github.com/spring-cloud-samples/bookstore-service-broker

And modified to remove kubernetes detection

## Running the service broker

```bash
./gradlew assemble (Java 8)

cf push -f deploy/cloudfoundry/manifest.yml
```

And make note of the route e.g.  
 `bookstore-service-broker.ocp-ic-prod.kubecf.dev`

And now to test  
`
curl https://bookstore-service-broker.ocp-ic-prod.kubecf.dev/v2/catalog -u admin:supersecret
  {"services":[{"id":"bdb1be2e-360b-495c-8115-d7697f9c6a9e","name":"bookstore","description":"A simple book store service","bindable":true,"plan_updateable":false,"plans":[{"id":"b973fb78-82f3-49ef-9b8b-c1876974a6cd","name":"standard","description":"A simple book store plan","free":true}],"tags":["book-store","books", "sample"]}]}
`

## Registration of service broker


```bash
cf create-service-broker bookstore admin supersecret https://bookstore-service-broker.ocp-ic-prod.kubecf.dev

cf enable-service-access bookstore
```

View broker in marketplace
```bash
cf marketplace

cf marketplace -s bookstore
```

## Create service

```bash
cf create-service bookstore standard my-bookstore
```

View service
```bash
cf service my-bookstore
```

## Create service key

```bash
cf create-service-key my-bookstore my-bookstore-binding
```

View service key
```bash
cf service-key my-bookstore my-bookstore-binding
```

And set variables:
```bash
export password=rK0lfzRrWwfy
export uri=https://bookstore-service-broker.ocp-ic-prod.kubecf.dev/bookstores/39171b3e-7dac-4344-a7a5-627fa6ee7c8c
export username=d16f98a7-fe1a-4a81-9271-ca691f0038f8
```

Test api calls
```bash
# Add book
curl {$uri}/books -u {$username}:{$password} -H "Content-Type: application/json" -X PUT -d '{"isbn":"978-1617292545","title":"Spring Boot in Action", "author":"Craig Walls"}'

# Add book
curl {$uri}/books -u {$username}:{$password} -H "Content-Type: application/json" -X PUT -d '{"isbn":"978-1784393021","title":"Learning Spring Boot", "author":"Greg L. Turnquist"}'

# List books
curl {$uri} -u {$username}:{$password} -H "Content-Type: application/json"

# Delete book
curl {$uri}/books/e44db6d7-506a-48e4-9446-44301dd559e6 -u {$username}:{$password} -H "Content-Type: application/json" -X DELETE

# List books
curl {$uri} -u {$username}:{$password} -H "Content-Type: application/json"
```

## Undo installation
```bash
cf delete-service-key my-bookstore my-bookstore-binding
cf delete-service my-bookstore
cf delete bookstore-service-broker -r
cf delete-service-broker bookstore
```

# Original deployment steps

(Deploy to cloud foundry)[deploy/cloudfoundry/README.adoc[deploy to Cloud Foundry]
