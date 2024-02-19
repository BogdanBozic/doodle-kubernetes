# doodle-kubernetes

## Table of contents:

- Environment
- Infinispan
- KeyCloak
- Postgresql
- Cert-manager
- Word from the author

### Environment

All the resources have been provisioned onto a local Minikube cluster.

### OLM (Operator Lifecycle Manager)

OLM is responsible for handling other operators in the cluster, provided that they have a subscription to the OLM. It handles updates, reconciliation of resources and in general almost completely removes need for human involvement for managing the operators.

Manages the Infinispan and KeyCloak operators here.

#### Installation

Installation is done with a script called install.sh. Provided by operatorhub.io, it installs the operator in the cluster from the local machine using kubeconfig file to communicte with the cluster.

### Infinispan

Installed with its operator. Logging set to Debug. Accessible with port-forwarding only.

### KeyCloak

Installed with its operator. Features cache-ispn.xml file that is used to manually create data for the configmap that it will use for custom configuration. It is needed in order to point KeyCloak to use external Infinispan, not the embedded one. CR is extended to provide environment variables needed for this to work. Except that it doesn't work here. 

#### Manual intervention needed

Once Infinispan is deployed, we need to copy the secret's content with the user credentials to the "keycloak" namecpace where KeyCloak is deployed so KeyCloak could authenticate with Infinispan.

#### Exposing the endpoint

Since the setup is on Minikube, editing the /etc/hosts file was enough for this job. In real-life situation, this would have been done with Ingress and a load balancer.

### Postgresql

The chosen database. KeyCloak properly connects to it.

#### Manual intervention needed

Once Postgresql is deployed, we need to copy the secret's content with the credentials to a new secret in the "keycloak" namespace where KeyCloak is deployed. 

### Cert-manager

Used for creating a self-signed certificate for development use. Had this been a production-ready application, letsencrypt would have been used.

### Word from the author

Postgres is installed with Helm. I know it has some operators, but since those are not of operatorhub.io, I didn't want to mix them.

Cert-manager is working properly.

Infinispan loads properly and can be accessed with port-forwarding.

KeyCloak properly connects to the database, uses a proper self-signed certificate, but doesn't connect to the remote Infinispan cluster. I have run out of time to debug it further, so I am leaving it as it is. CR has been edited to provide additional env vars, cache.ispn.xml is edited as per the instructions, but I am still missing something.

---

Overall, this is my first time setting up operators, as well as running Java applications, so almost all the assignment was new to me. A great deal of time I spent reading through the documentation for the required resources.