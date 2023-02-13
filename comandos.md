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
