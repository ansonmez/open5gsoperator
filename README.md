Openshift 4.6 
# oc new-project open5gsoperator-system

Enable SCTP  load-sctp-module.yaml
Networkattachmentdefinition network-attachment-definitions.yaml


oc adm policy add-scc-to-user anyuid -z default
oc adm policy add-scc-to-user hostaccess -z default
oc adm policy add-scc-to-user hostmount-anyuid -z default
oc adm policy add-scc-to-user privileged -z  default


oc create -f deploy/crds/open5gs.anil.redhat_open5gs_crd.yaml

oc create -f deploy/service_account.yaml
oc create -f deploy/role.yaml
oc create -f deploy/role_binding.yaml
oc create -f deploy/operator.yaml2

oc create -f open5gs_cr.yaml
