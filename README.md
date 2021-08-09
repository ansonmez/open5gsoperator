Tested on Openshift 4.6 
```

# oc new-project open5gsoperator-system

Enable SCTP  load-sctp-module.yaml
# oc apply -f load-sctp-module.yaml 

Add network attachment definition, while adding secondary interface check Openshift workers and identify interface name, and adjust with definition in network-attachment-definitions.yaml , ip address attached to secondary interface has to be reachable/routeable from primary interfaces, smf will be communicating with upf (secondary interface will be attached to upf cnf)
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


check operatorhub in OCP and search for open5gs
while creating open5gs instance change networkattachmentdefinition in custom resource 
netattachdefinition should match with network-attachment-definitions.yaml
The custom resource below will be populated automated in yaml view in Openshift 


apiVersion: open5gs.anil.redhat/v1alpha1
kind: Open5gs
metadata:
  name: anil-open5gs
  namespace: open5gsoperator-system
spec:
  open5gs_vars:
    amf:
      mcc: 208
      mnc: 93
      tac: 70
    dnn: internet
    dnn2: arvr
    k8s:
      advertise: 192.168.50.200
      interface: eth0
    netattchdefinition: testuserplane
    open5gs:
      image:
        pullpolicy: IfNotPresent
        repository: registry.gitlab.com/infinitydon/registry/open5gs-aio
        tag: v2.2.2
    smf:
      gtpc:
        interface: eth0
      gtpu:
        interface: eth0
      pfcp:
        interface: eth0
    upfmain:
      addr: 192.168.50.200
    upfmec:
      addr: 192.168.50.201
    webui:
      image:
        pullpolicy: IfNotPresent
        repository: registry.gitlab.com/infinitydon/registry/open5gs-webui
        tag: v2.2.2
      ingress:
        enabled: false
        hosts:
          - name: open5gs-epc.local
            paths:
              - /
            tls: false
```

