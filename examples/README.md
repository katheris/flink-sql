# Flink SQL Examples

This directory contains assets for running Flink SQL examples that start with one or more streams of 
data in Apache Kafka topics, run a Flink SQL job and result in one or more streams of data sent 
to Apache Kafka topics.

## Running the examples

The steps to run each example are very similar (TODO add steps for running Flink SQL):

1. Start minikube with the following resources.

   ```
   MINIKUBE_CPUS=4
   MINIKUBE_MEMORY=16384
   MINIKUBE_DISK_SIZE=25GB
   ```

2. Create a `flink` namespace:
   ```
   kubectl create namespace flink
   ```

3. Apply the Strimzi QuickStart:
   ```
   kubectl create -f 'https://strimzi.io/install/latest?namespace=flink' -n flink
   ```
4. Create a Kafka
   ```
   kubectl apply -f https://strimzi.io/examples/latest/kafka/kraft/kafka-single-node.yaml -n flink 
   ```
5. Install cert-manager (this creates cert-manager in a namespace called `cert-manager`):
   ```
   kubectl create -f https://github.com/jetstack/cert-manager/releases/download/v1.8.2/cert-manager.yaml
   ```
6. Deploy Flink Kubernetes Operator 1.8.0 (the latest stable version):
   ```
    helm repo add flink-operator-repo https://downloads.apache.org/flink/flink-kubernetes-operator-1.8.0/
    helm install flink-kubernetes-operator flink-operator-repo/flink-kubernetes-operator -n flink
   ```
7. Build the image like this:
   ```
   mvn clean package && minikube image build examples-data-generator -t data-generator:latest
   ```

8. Create the data generator Kubernetes Deployment for the specific example you are running:
   ```
   kubectl apply -f examples/<EXAMPLE_DIRECTORY>/data-generator.yaml -n flink
   ```

# Running the examples with Avro records

You can also run the examples with source topics that contain Avro records, rather than CSV records.
To run with Avro:
1. In `examples/<EXAMPLE_DIRECOTRY>/data-generator.yaml` uncomment the `USE_APICURIO_REGISTRY` and `REGISTRY_URL` env vars.
2. TODO: how to specify the Avro version of the SQL statements
3. Follows the steps to run the example shown above, but after creating the `flink` namespace run the following to start up Apicurio Registry:
  ```
  kubectl apply -f examples/apicurio-registry.yaml -n flink
  ```

This version of the demo will start Apicurio Registry with a service to access the API.
The source topics for the example will contain Avro records.
You can view the Apicurio Registry UI by running `kubectl port-forward service/apicurio-registry-api 8080 -n flink` and visiting http://localhost:8080/ui in a browser.
