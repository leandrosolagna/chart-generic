[![CircleCI](https://circleci.com/gh/leandrosolagna/chart-generic.svg?style=shield)](https://circleci.com/gh/leandrosolagna/chart-generic)

## Generic Helm Chart

A generic helm chart for any possible project.

TL;DR: Helm automates the creation of kubernetes manifests.

Some commands:

`$helm lint`

`$helm package .`

`$helm install RELEASE_NAME PACKAGE_CREATED.tgz --dry-run --debug`

`$helm uninstall <RELEASE_NAME> --namespace <NAMESPACE>`

Upgrading:

`$helm upgrade --install --namespace <NAMESPACE> <RELEASE_NAME> <PACKAGE_CREATED.tgz>`

### Parameters
The following tables lists the configurable parameters of the generic chart and their default values.

| Parameter | Description | Default |
|--- | --- | --- |
| `replicaCount` | Number of pods to be spawned | `1` |
| `image.repository` | Repository path to image | `private.repo.local:5000/path/to/image` |
| `image.tag` | Docker image tag | `0.0.0` |
| `image.pullPolicy` | Policy to download the image | `IfNotPresent` |
| `image.pullSecrets.enabled` | Enable pull secrets to download the image in local cluster | `false` |
| `probes.enabled` | Enable custom probes | `false` |
| `probes.livenessPath` | Path to liveness route | `/internal/liveness` |
| `probes.readinessPath` | Path to readiness route | `/internal/readiness` |
| `probes.liveness.initialDelaySeconds` | Number of seconds after the container has started before liveness probes are initiated | `0` |
| `probes.liveness.timeoutSeconds` | Number of seconds after which the probe times out | `1` |
| `probes.liveness.periodSeconds` | How often (in seconds) to perform the probe | `10` |
| `probes.liveness.successThreshold` | Minimum consecutive successes for the probe to be considered successful after having failed | `1` |
| `probes.liveness.failureThreshold` | When a probe fails, Kubernetes will try failureThreshold times before giving up. Giving up in case of liveness probe means restarting the container | `3` |
| `probes.readiness.initialDelaySeconds` | Number of seconds after the container has started before readiness probes are initiated | `0` |
| `probes.readiness.timeoutSeconds` | Number of seconds after which the probe times out | `1` |
| `probes.readiness.periodSeconds` | How often (in seconds) to perform the probe | `10` |
| `probes.readiness.successThreshold` | Minimum consecutive successes for the probe to be considered successful after having failed | `1` |
| `probes.readiness.failureThreshold` | When a probe fails, Kubernetes will try failureThreshold times before giving up. In case of readiness probe the Pod will be marked Unready | `3` |
| `configmap.enabled` | Enables ConfigMap manifest | `false` |
| `configmap.variables` | An array of environment variables consisting of `keys` and `values` | `{}` |
| `service.enabled` | Enables Service manifest | `false` |
| `service.port` | Exposes the Kubernetes service on the specified port within the cluster | `80` |
| `service.targetPort` | Port on which the service will send requests to | `80` |
| `ingress.enabled` | Enables Ingress manifest | `false` |
| `ingress.host` | Host name for access outside the cluster | `local.web.image` |
| `ingress.tls` | Enables TLS in Ingress | `true` |
| `cronjob.enabled` | Enables cronjob manifest | `false` |
| `cronjob.schedule` | Sets the time to run the job | `"* * * * *"` |
| `cronjob.image` | Image for the cronjob pod | `private.repo.local:5000/path/to/image:latest` |
| `cronjob.restartPolicy` | Restart policy for all containers within the pod | `Never` |
| `cronjob.command` | Commands to run | `- echo "Testing cronjob"` |


### Setting variables in ConfigMap

If you look at the code, you will see that the `helpers` file replace everything that has `-` (dash) to `_` (underscore) and it also convert all the characters to uppercase when creating the variable to be used by the application.

**Examples**:
 - user-database: test
 - passwrd-database: password

So, if you need to separate the words, please keep this in mind.


### Naming pull secret in Deployment

For local deployment, you will need to enable `imagePullSecrets` and create the secret to pull an image from a **private docker registry**.

You may do this using the following command:

`$kubectl create secret generic <HELM_RELEASE_NAME>-image-pull --from-file=.dockerconfigjson=${HOME}/.docker/config.json --type=kubernetes.io/dockerconfigjson --namespace <NAMESPACE_NAME>`

The rule for the name is: `{{ .Release.Name }}-image-pull`

`{{ .Release.Name }}` is the name that you give when deploying the helm chart.
