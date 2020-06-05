To Deploy custom HAProxy template:

```
$ oc create cm haproxy-template -n openshift-ingress --from-file=haproxy-config.template  
$ oc scale -n openshift-ingress-operator deployment ingress-operator --replicas=0
$ oc set volume deployment router-default -n openshift-ingress --add --name=haproxy-template --mount-path=/var/lib/haproxy/conf/haproxy-config.template --sub-path=haproxy-config.template --type=configmap --configmap-name=haproxy-template
```

Set route annotation to change the behavior to pass the header through rather than set the header newly.  You do not need all of these together, just the ones you want.  

```
$ oc annotate route <route_name> haproxy.router.openshift.io/set-forwarded-headers="if-none"
```

To revert changes:
```
$ oc scale -n openshift-ingress-operator deployment ingress-operator --replicas=1
```

To make changes to the config and update the k8s object from your current working file:

```
$ oc create cm haproxy-template --from-file=haproxy-config.template --save-config --dry-run -o yaml | oc apply -f -
```

Delete the existing pods to rebuild it with the new configmap.
