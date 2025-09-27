# mosquitto-kubernetes

## Copyright

(c) 2025 Mark Himsley

## Description

This project is designed as a quick how-to describing creating an [Eclipse Mosquitto](https://mosquitto.org/) MQTT server running in [Kubernetes](https://kubernetes.io/).

This repository also contains example Kubernetes [kubectl kustomize](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_kustomize/) manifest fragments to create a Mosquitto MQTT broker instance running as a Deployment in a [Kubernetes cluster](https://kubernetes.io/docs/concepts/overview/components/). The example Kubernetes manifest fragments assume that some highly available NFS storage is available for storing persistent data, and that the Mosquitto MQTT broker services will be available to connect to from outside of the Kubernetes cluster.

Please make sure you are familiar with [Eclipse Mosquitto](https://mosquitto.org/) and [Kubernetes](https://kubernetes.io/).

## Configuration

**Note:** the example username and password `src/kustomize/override/example/mosquitto-config/password.txt` file is `admin` and `password`. This must be changed following the instructions below.

**Note:** the example configuration expects a certificate and key to be available in a secret called `mosquitto-certs`. This could be created by a certificate manager or could be created manually, but that is out of scope for this demonstration project.

* Make a new kubectl kustomize override directory by copying the directory [`src/kustomize/override/example`](src/kustomize/override/example) and its contents to a new directory within the `override` directory.

  Replace `<NEW_DIRECTORY_NAME>` with the name you wish to give to your override configuration.

  ```code
  pushd src/kustomize/override
  cp -va example <NEW_DIRECTORY_NAME>
  popd
  ```

* Using the [`mosquitto_passwd`](https://mosquitto.org/man/mosquitto_passwd-1.html) application, replace the file `src/kustomize/override/<NEW_DIRECTORY_NAME>/mosquitto-config/password.txt`

  ```code
  pushd src/kustomize/override/<NEW_DIRECTORY_NAME>
  mosquitto_passwd -c mosquitto-config/password.txt <USER_NAME>
  popd
  ```

  You will be asked for a password for your user name.

  If you want to add other users & passwords then repeat the `mosquitto_passwd` command omitting the `-c` parameter, which overwrites and creates a new password file.

* Edit the file [`persistentVolume.yaml`](src/kustomize/override/example/persistentVolume.yaml) to point to your persistent storage.

* If you want to expose the MQTT or MQTTS service outside of the cluster then edit the [`service-mqtts-patch.yaml`](src/kustomize/override/example/service-mqtts-patch.yaml) and/or [`service-mqtts-patch.yaml`](src/kustomize/override/example/service-mqtt-patch.yaml) files to set the correct load balancer IP address, and uncomment the patch path(s) in the [`kustomization.yaml`](src/kustomize/override/example/kustomization.yaml) file.

## Deploying to Kubernetes

Check that the kustomized yaml manifests are correct by running `kubectl kustomize` against your override directory.

```code
kubectl kustomize src/kustomize/override/<NEW_DIRECTORY_NAME>
```

When you believe the output is correct, apply that to your kubernetes cluster.

```code
kubectl kustomize src/kustomize/override/<NEW_DIRECTORY_NAME> | kubectl apply -f - --context <cluster-name>
```

## Warranty

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
