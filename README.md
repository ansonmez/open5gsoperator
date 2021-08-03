Openshift 4.6 
```

# oc new-project open5gsoperator-system

Enable SCTP  load-sctp-module.yaml
# oc apply -f load-sctp-module.yaml 

Add network attachment definition 
# oc apply -f  network-attachment-definitions.yaml 

enable SCC for open5gs #  admiting i am lazy i have not spend time for making it scc free
# oc adm policy add-scc-to-user anyuid -z default
# oc adm policy add-scc-to-user hostaccess -z default
# oc adm policy add-scc-to-user hostmount-anyuid -z default
# oc adm policy add-scc-to-user privileged -z  default

add catalogsource
# cat <<EOF | oc apply -f -
apiVersion: operators.coreos.com/v1alpha1
kind: CatalogSource
metadata:
  name: anil-operators
  namespace: openshift-marketplace
spec:
  sourceType: grpc
  image: quay.io/asonmez/open5gs-index:1.0.0
  displayName: Anil Operators
  publisher: Anil Sonmez
  updateStrategy:
    registryPoll:
      interval: 60m
EOF
```

