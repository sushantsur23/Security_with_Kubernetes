Network policies

IN a kubernetes cluster which is set up with internal cluster network, By default all the pods can talk to each other. pods can be in different namespace. they can make API request to each other.

kbectl get pods -A -o wide
we will see the pod in differnt namespace will have same CIDR block

if we have front end, backend and database namespace then frontend namespace should not be able to talk to the database namespace. they share same cluster network we need a firewall to protect here to protect database pod. Kubernetes helps with network policies.

we can defie network policies to the pods. 
which pod should only have access to our database pod.



