# H2O.ai Deployments

## H2O-3 Cluster (OSS)
H2O is an open source, in-memory, distributed, fast, and scalable machine learning and predictive analytics platform that allows you to build machine learning models on big data and provides easy productionalization of those models in an enterprise environment.

### H2O-3 Deployment Steps:
NOTE: The deployment steps assume that you have already set up a Kubernetes cluster and that `kubectl` and `ksonnet` have been installed/setup properly within your environment.

1. Create a Docker image for Kubeflow to ingest. Files are located under `dockerfiles` data_directory
  * Run Command: `docker build -t <personal_repository>/h2o3-kubeflow:latest -f Dockerfile.h2o3` from the `dockerfiles` directory

2. Push the new docker image to a remote repository that Kubeflow can access later `docker push <personal_repository>/h2o3-kubeflow:latest`

3. Create a new ksonnet app in your local environment
  * Run Command: `ks init <name_of_new_app>` and `cd <name_of_new_app>`

4. Grab Ksonnet registry and install necessary packages
    ```
    # add ksonnet registry to app containing all the kubeflow manifests as maintained by Google Kubeflow team
    ks registry add kubeflow https://github.com/kubeflow/kubeflow/tree/master/kubeflow
    # add ksonnet registry to app containing all the h2o component manifests
    ks registry add h2o-kubeflow <this_github_repo/h2o-kubeflow>
    ks pkg install kubeflow/core
    ks pkg install kubeflow/tf-serving
    ks pkg install kubeflow/tf-job
    ks pkg install kubeflow/h2oai-h2o3
    ```
5. Deploy the H2O-3 Cluster to your Kubernetes cluster
    ```
    ks prototype use io.ksonnet.pkg.h2oai-h2o3 h2o3-cluster \
    --name h2o3-cluster \
    --namespace kubeflow \
    --memory 1 \
    --cpu 1 \
    --replicas 2 \
    --model_server_image <location_of_docker_image>

    ks apply <my_environment_name> -c h2o3-cluster
    ```
6. You will be able to see the exposed ip address and port for the cluster using: `kubectl get svc`

## Driverless AI (Enterprise)
