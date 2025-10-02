# Example S3-to-Kafka App based on Camel Kamelets

Example app to send objects from S3 to a given Kafka topic. Based on Apache Camel Kamelets. 

1. Create a Kubernetes cluster
2. Create a Kafka Cluster (e.g. via [Strimzi](https://strimzi.io/quickstarts/))
3. Create a S3 Bucket
4. Build and deploy the app
   ```
   mvn package -Dquarkus.container-image.registry=<your-container-registry> -Dquarkus.container-image.group=<your-repo-org> 
   ```
5. Create a secret with credentials for the S3 bucket
   ```
   kubectl create secret generic aws-access --from-literal key-id=<IAM user key-id> --from-literal access-key=<IAM user key>
   ```
6. Apply manifests and adjust before (e.g. Bucket name or AWS region of S3 Bucket):
   ```
   kubectl apply -f target/kubernetes/kubernetes.yml
   ```
7. Test if messages get handled/logged: 
   1. Add some files to the S3 Bucket
   2. Check if the files get sent to Kafka
      ```
      kubectl -n kafka run kafka-consumer -ti --image=quay.io/strimzi/kafka:0.48.0-kafka-4.1.0 --rm=true --restart=Never -- bin/kafka-console-consumer.sh --bootstrap-server my-cluster-kafka-bootstrap.kafka:9092 --topic my-topic --from-beginning
      ```