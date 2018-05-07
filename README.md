# ansible-ocp-project-bootstrap

## Service Account Configuration

Automated provisioning is easiest when using a dedicated serviceAccount with perpetual token:

```
export GF_PROJECT="project_to_host_your_sa"

oc login -u system:admin
oc project ${GF_PROJECT}
oc create sa sa-projectmgr
oc adm policy add-cluster-role-to-user cluster-admin system:serviceaccount:${GF_PROJECT}:sa-projectmgr

# Extract persistent token and save to ~/.token:

oc get secret | grep sa-projectmgr-dockercfg | tail -1 | \
	awk '{system("oc get secret " $1 " -o json")}' | \
	python -c 'import sys, json; print json.load(sys.stdin)["metadata"]["annotations"]["openshift.io/token-secret.value"]' > ~/.token

```

## Override parameters on execution

List variable overrides require wrapping in valid JSON:

```
ansible-playbook site.yaml -e '{"ocp_project_admin_users":["test1","test2"]}'
```
