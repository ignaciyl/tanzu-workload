# Crear Workload de pruebas
``` 
tanzu apps workload create test-tanzu --app test-tanzu --type web --local-path . --source-image harbor.lab.ejie.local/test/test --registry-ca-cert ./ca.pem --registry-username admin --registry-password xxxxxxx

tanzu apps workload create test-tanzu --app test-tanzu --type web --local-path . --source-image harbor.lab.ejie.local/test/test --registry-ca-cert ./ca.pem --registry-username admin --registry-password xxxxxxx   --label apps.tanzu.vmware.com/has-tests=true 

tanzu apps workload create test-tanzu --app test-tanzu --type web --local-path . --source-image harbor.lab.ejie.local/test/test --registry-ca-cert ./ca.pem --registry-username admin --registry-password xxxxxxx   --label apps.tanzu.vmware.com/has-tests=true --namespace development
``` 

Falta Supply Chain <-----

Lista supply-chains
``` 
tanzu apps cluster-supply-chain list
NAME                         READY   AGE
scanning-image-scan-to-url   Ready   3d4h
source-test-scan-to-url      Ready   3d4h
``` 

Lista workloads
``` 
nacho@tanzu-virtual-machine:~/tap$ tanzu apps workloads list
NAME         TYPE   APP          READY                 AGE
test-tanzu   web    test-tanzu   SupplyChainNotFound   24m
``` 


Detalle de un workload
``` 
tanzu -n development apps workloads get test-tanzu
📡 Overview
   name:   test-tanzu
   type:   web

💾 Source
   type:    source image
   image:   harbor.lab.ejie.local/test/test:latest@sha256:98d3be1f0346cce52761ec3ef299c4402bd9f6e036d334413478da8b15233e79

📦 Supply Chain
   name:   <none>

   Supply Chain resources not found.

🚚 Delivery

   Delivery resources not found.

💬 Messages
   Workload [SupplyChainNotFound]:   no supply chain found where full selector is satisfied by labels: map[app.kubernetes.io/part-of:test-tanzu apps.tanzu.vmware.com/workload-type:web]

No pods found for workload.

To see logs: "tanzu apps workload tail test-tanzu"
``` 


Obtener el estado un workload
``` 
tanzu apps workload get test-tanzu -n development
``` 

https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-cli-plugins-apps-debug-workload.html

``` 
Workload ?[2m[TemplateRejectedByAPIServer]?[0m:   unable to apply object [development/test-tanzu] for resource [source-provider] in supply chain [source-test-scan-to-url]: failed to get unstructured [development/test-tanzu] from api server: imagerepositories.source.apps.tanzu.vmware.com "test-tanzu" is forbidden: User "system:serviceaccount:development:default" cannot get resource "imagerepositories" in API group "source.apps.tanzu.vmware.com" in the namespace "development"
``` 

Asignar el rol(funcionó en Linux) y lanzar el comando de nuevo 
``` 
$ tanzu rbac binding add --user administrator@baterav2.infra --role app-editor --namespace development
Created RoleBinding 'app-editor' in namespace 'development'
Added User 'administrator@baterav2.infra' to RoleBinding 'app-editor' in namespace 'development'
Created ClusterRoleBinding 'app-editor-cluster-access'
Added User 'administrator@baterav2.infra' to ClusterRoleBinding 'app-editor-cluster-access'

$ tanzu rbac binding add --user system:serviceaccount --role app-editor --namespace development
Added User 'system:serviceaccount' to RoleBinding 'app-editor' in namespace 'development'
Added User 'system:serviceaccount' to ClusterRoleBinding 'app-editor-cluster-access'

$ tanzu rbac binding add --user system:serviceaccount:development:default --role app-editor --namespace development
Added User 'system:serviceaccount:development:default' to RoleBinding 'app-editor' in namespace 'development'
Added User 'system:serviceaccount:development:default' to ClusterRoleBinding 'app-editor-cluster-access'

$tanzu rbac binding add --user wcp:10.230.24.18:administrator@baterav2.infra --role app-editor --namespace development
Added User 'wcp:10.230.24.18:administrator@baterav2.infra' to RoleBinding 'app-editor' in namespace 'development'
Added User 'wcp:10.230.24.18:administrator@baterav2.infra' to ClusterRoleBinding 'app-editor-cluster-access'

tanzu apps workload create test-tanzu --app test-tanzu --type web --local-path . --source-image harbor.lab.ejie.local/test/test --registry-ca-cert ./ca.pem --registry-username admin --registry-password xxxxxxxxx   --label apps.tanzu.vmware.com/has-tests=true --namespace development
``` 

Añadir la credencial para subir el objeto al registro
``` 
tanzu secret registry add registry-credentials --server harbor.lab.ejie.local/test/test --username admin --password Harbor12345 --namespace development
``` 


Añadir reglas de RBAC para autorizar al service account en namespace de developer
``` 
cat <<EOF | kubectl -n development apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: tap-registry
  annotations:
    secretgen.carvel.dev/image-pull-secret: ""
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: e30K
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: default
secrets:
  - name: registry-credentials
imagePullSecrets:
  - name: registry-credentials
  - name: tap-registry
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: default-permit-deliverable
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: deliverable
subjects:
  - kind: ServiceAccount
    name: default
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: default-permit-workload
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: workload
subjects:
  - kind: ServiceAccount
    name: default
EOF

``` 

Borrar un workload
```
tanzu apps workloads delete test-tanzu -n development
tanzu apps workload create test-tanzu --app test-tanzu --type web --local-path . --source-image harbor.lab.ejie.local/test/test --registry-ca-cert ./ca.pem --registry-username admin --registry-password Harbor12345   --label apps.tanzu.vmware.com/has-tests=true --namespace development


tanzu apps workload create test-tanzu --app test-tanzu --type web --local-path . --git-repo https://github.com/spring-projects/spring-petclinic.git --git-branch main --source-image harbor.lab.ejie.local/test/test --registry-ca-cert ./ca.pem --registry-username admin --registry-password Harbor12345   --label apps.tanzu.vmware.com/has-tests=true --namespace development

https://github.com/spring-projects/spring-petclinic.git


  --git-repo https://github.com/vmware-tanzu/application-accelerator-samples \
  --sub-path tanzu-java-web-app \
  --git-branch main \

tanzu apps workload create test-tanzu \
  --app test-tanzu \
  --git-repo https://github.com/spring-projects/ \
  --git-branch main \
  --type web \
  --label apps.tanzu.vmware.com/has-tests=true \
  --label app.kubernetes.io/part-of=tanzu-java-web-app \
  --namespace development \
  --yes

```