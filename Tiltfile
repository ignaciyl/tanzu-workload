REGISTRY = os.getenv("REGISTRY", default='harbor.lab.ejie.local')
REGISTRY-REPOSITORY = os.getenv("REGISTRY-REPOSITORY", default='test')
SOURCE_IMAGE = os.getenv("SOURCE_IMAGE", default='source-image-location')
LOCAL_PATH = os.getenv("LOCAL_PATH", default='local-path')
NAMESPACE = os.getenv("NAMESPACE", default='default')
OUTPUT_TO_NULL_COMMAND = os.getenv("OUTPUT_TO_NULL_COMMAND", default=' > /dev/null ')

k8s_custom_deploy(
    'workload-name',
    apply_cmd="tanzu apps workload apply -f path-to-workload-yaml --debug --live-update" +
        " --source-image " + REGISTRY/REGISTRY-REPOSITORY +
        " --local-path " + LOCAL_PATH +
        " --namespace " + NAMESPACE +
        " --yes " +
        OUTPUT_TO_NULL_COMMAND + 
        " && kubectl get workload workload-name --namespace " + NAMESPACE + " -o yaml",
    delete_cmd="tanzu apps workload delete -f path-to-workload-yaml --namespace " + NAMESPACE + " --yes" ,
    deps=['pom.xml', './target/classes'],
    container_selector='workload',
    live_update=[
        sync('./target/classes', '/workspace/BOOT-INF/classes')
    ]
)

k8s_resource('workload-name', port_forwards=["8080:8080"],
    extra_pod_selectors=[{'carto.run/workload-name': 'workload-name', 'app.kubernetes.io/component':'run'}])

allow_k8s_contexts('CONTEXT-NAME')

