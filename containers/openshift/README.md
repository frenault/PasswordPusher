# Installation Through Openshift
----- IN PROGRESS -----
Installing Password Pusher should be simple. For Openshift installations, the repository includes a bundle of manifests configured to set up a working copy of Password Pusher.
On my work environment we dont uses namspaces we uses Projects instead. Fell free to change accordinglly with your needs.
Openshift clusters are complex and may differ a lot. The bundled manifests expects the following:
- `ginx ingress-controller` deployed for the cluster or namespace
- `cert-manager` installed and configured with a `cluster-issuer` named `letsencrypt-prod`

When deployed, the following kubernetes objects are provisioned:
* project: `pwpush`
* deployment: `pwpush`
  * pods:
    * pwpush
    * postgres (when using the persistent deploy type)
* service: `pwpush`
* route: `pwpush-ingress`
* certificate: `pwpush.domain.tld` (will be renamed accordingly if following the documented steps in [deploy](#deploy)

# Deploy

Since Password Pusher supports both ephemeral and database-backed installations, instructions (and manifests) vary. See [Ephemeral](#ephemeral) & [Persistent](#persistent)

All deploy options need project, route and service defined 
oc apply -f containers/openshift/project.yaml
oc apply -f containers/openshift/route.yaml
oc apply -f containers/openshift/service.yaml

## Ephemeral
```
oc apply -f containers/openshift/namespace.yaml
oc apply -f containers/openshift/ephemeral_deploy.yaml
oc apply -f containers/openshift/route.yaml
oc apply -f containers/openshift/service.yaml
```

## Persistent
The kubernets  deploy which one this was based uses a all in one pod strategy. I kept for test purpose

## One Pod
This type of deploy requires the host path `/nfs/k8s/services/pwpush/data` already mounted on the node(s) expected to run the deployment. Most likely, you will want to modify that path in your own environment or reconfigure the persistent volume to use a different type of volume matching your cluster setup.

Modifications of the host path can be done by:
`sed -i 's|/nfs/k8s/services/pwpush/data|/your/path/here|g' containers/openshift/pv.yaml`

Modifications to the type of volume is left to the reader to implement. The relevant file is `containers/openshift/pv.yaml`

Applying all manifests
```
oc apply -f containers/openshift/namespace.yaml
oc apply -f containers/openshift/pv.yaml
oc apply -f containers/openshift/pvc.yaml
oc apply -f containers/openshift/persistent_deploy.yaml
oc apply -f containers/openshift/service.yaml
oc apply -f containers/openshift/ingress.yaml
```
## Persistent One Pod
This type of deploy requires the host path `/nfs/k8s/services/pwpush/data` already mounted on the node(s) expected to run the deployment. Most likely, you will want to modify that path in your own environment or reconfigure the persistent volume to use a different type of volume matching your cluster setup.

Modifications of the host path can be done by:
`sed -i 's|/nfs/k8s/services/pwpush/data|/your/path/here|g' containers/openshift/pv.yaml`

Modifications to the type of volume is left to the reader to implement. The relevant file is `containers/openshift/pv.yaml` 
Applying all manifests
```
oc apply -f containers/openshift/namespace.yaml
oc apply -f containers/openshift/pv.yaml
oc apply -f containers/openshift/pvc.yaml
oc apply -f containers/openshift/persistent_deploy.yaml
oc apply -f containers/openshift/service.yaml
oc apply -f containers/openshift/ingress.yaml
```
