# Palo Alto Ignite Conference 2019

## Container and Orchestration Security in Data Center and Multi-Cloud Environments

- Docker uses Linux features of namespaces and cGroups.
- Containerization breaks vulnerability scanning from a network point of view, since networks are virtualized/abstracted and containers are ethemeral.

_Software Supply Chain_
- Start with latest baseline image
- Inventory all compontents
- Use controlled repositories
- Keep a bill of materials for known vulnerabilities
- Scan the image before using it

_Infrastructure_
 - Network isolation
 - Seperate clusters for access to priv. resources (e.g. seperate host/IAM roles for BigQuery).
 - Restricted etcd access
 - Network controls for ingress/egress
 - Run without priviledge
 - Utilize seperate namespaces/service accounts
 
 _Runtime_
 - Set profiles for enformcement tools (appamror, seccomp)
 
 _Secrets_
 RCE vulnerability can be used to promote a container to a node, and join the cluster, enabling querying of secrets
 
 

42% of K8 Pods are accessible from the Internet
Average container lifetime is four minutes
Never use K8 secrets
