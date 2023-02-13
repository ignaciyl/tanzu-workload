# Crear Workload de pruebas
tanzu apps workload create test-tanzu --app test-tanzu --type web --local-path . --source-image harbor.lab.ejie.local/test/test --registry-ca-cert ./ca.pem --registry-username admin --registry-password Harbor12345

Falta Supply Chain <-----
