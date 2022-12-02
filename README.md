# Automated ELK Rollout on Kubernetes
This Ansible Demo will roll out an ELK-Stack on an Openshift (or Microshift) Kubernetes Cluster or Single Node. 
It will use Twitter as an Import for Logstash and feed Tweets containing a given Keyword into Elasticsearch

## Reguirements 
### Kubernetes Setup
 - Twitter API Access Key (create one on https://developer.twitter.com)
 - Openshift, OKD or Microshift Setup (Cluster or single Node). 
 - A default Storage class on this Setup to provide a PV to the Elastic-Pod
 - The ECK-Operator (https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-deploy-eck.html). This code has been tested with the ECK-Operator 2.5.0

The demo requires the API "route.openshift.io/v1" with the "kind: Route" on your Setup to allow Access to the Kibana Frontend. If you run a kubernetes Distribution without it, you will need to rewrite the task "name: Create kiba2-route" to use a kubernetes ingress rule for Kibana rather than an Openshift Route

### Client
 - Linux System with Ansible (2.11+), 
 - oc/kubectl Client
 - Ansible Collection kubernetes.core 

## Variables in vars.yml:
 - elk_ns: Namespace für the Setup (must exist)
 - fqdn: Domain-Name of your Setup as base for the Route. The Kibana Page will then be accessible on https://kiba2.{{ fqdn }}
 - tw_keyword: The Twitter Keyword you want to index
 - app_key, app_secret, token_key & token_secret: Your Twitter API credentials

## How to Run
 - Log in to your Open/Microshift Setup (preferably with "export KUBECONFIG=/path/to/your/kubeconfig")
 - Run elk2-rollout.yml with ansible-playbook or ansible-navigator
 - The last task will echo the Password of the User "elastic" on the CLI
 - Log into Kibana (Url as above) as user "elastic" with the Password 
 - Create a new Index based on the pattern "twitter_elastic_example*" with @timestamp as Time Filed

You can only create the index, after logstash sent the first tweet to elasticsearc. As long as there are no tweeds with your selected keyword coming in, there will be no index. So use a popular Keyword for your first test.
Once you're done, the playbook "cleanup.yml" will delete the Setup from your cluster.

Be aware: The current version will add your Twitter credentials into a config map, so Kubernetes Users with access to your namespace can see them. 

## ToDo:
 - Move the Twitter Credentials to a Kubernetes Secret instead of a config map.
 - Make the solution Available for AWX/Ansible Controller instead of the Cli

 v 1.0 2.12.22

