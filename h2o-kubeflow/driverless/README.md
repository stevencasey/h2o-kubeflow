# Driverless AI on Kubernetes and Kubeflow

This repo contains the necessary manifests to deploy Driverless AI as a
persistent server on Kubernetes running Kubeflow


## Quickstart

_The following commands use the ```io.ksonnet.pkg.driverless``` prototype to generate Kubernetes YAML for driverless,
and then deploys it to your Kubernetes cluster._

1. First, create a cluster and install the ksonnet CLI.
2. If you have not yet created a ksonnet application, do so using:
```
$ ks init <app-name>
$ cd <app-name>
```
3. Setup Kubeflow and install necessary packages:
```
# add registry to ksonnet and install manifest packages for kubeflow and driverless
ks registry add h2o-kubeflow github.com/<path_to_github_repo>
ks pkg install h2o-kubeflow/driverless
```
4. (OPTIONAL) Create ConfigMap Volume containing user configurations for Driverless AI, expectation is that will contain 2 files: config.toml and license.sig, where config.toml will have any user overrides for Driverless AI and license.sig contains the license code for Driverless AI. A sample config.toml is provided in this directory or [here](http://docs.h2o.ai/driverless-ai/latest-stable/docs/userguide/config_toml.html#sample-config-toml-file).
**NOTE**: all files inside the directory path will be loaded for consumption. EX. config.toml overrides authentication with local authentication (htpasswd), htpasswd file is contained in /path/to/configuration/files, then config.toml can consume the file at path /config/<name of htpasswd file>
```
kubectl create configmap driverless --from-file="/path/to/configuration/files/"
```
NOTE: path should not include file names, ConfigMap volume will ingest all files within the folder provided. Also, make sure to name the ConfigMap Volume the same as the deployment name so that the deployment can find the volume.

5. In the ksonnet application directory run the following:
```
# Expand prototype as a Jsonnet file, place in a file in the
# components/ directory. (YAML and JSON are also available)
ks prototype use io.ksonnet.pkg.driverless driverless \
--name driverless \
--namespace driverless \
--memory 1 \
--cpu 1 \
--gpu 0 \
--pvcSize 50 \
--configmap_name <configmap name> \
--model_server_image opsh2oai/h2oai-runtime
```
6. Deploy driverless on kubernetes:
```
$ ks apply [environment] -c driverless
# check if deployment was successful and get pod name for port forwarding
kubectl get deployments -n driverless
kubectl get pods -n driverless
kubectl port-forward <pod-name> 12345:12345
```
7. View Driverless AI GUI at https://127.0.0.1:12345
