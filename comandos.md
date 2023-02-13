# Crear Workload de pruebas
``` 
tanzu apps workload create test-tanzu --app test-tanzu --type web --local-path . --source-image harbor.lab.ejie.local/test/test --registry-ca-cert ./ca.pem --registry-username admin --registry-password xxxxxxx
``` 

Falta Supply Chain <-----

Lista supply-chains
``` 
tanzu apps cluster-supply-chain list
NAME                         READY   AGE
scanning-image-scan-to-url   Ready   3d4h
source-test-scan-to-url      Ready   3d4h
``` 
