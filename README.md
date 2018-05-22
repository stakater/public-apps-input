# stakater-apps

This is stakater apps repo for applying landscaper charts via pipeline.

### How to apply manually

```bash
landscaper apply --dir manifests --namespace <namespace-name>
```
### Convention

Landscaper applies these manifests in same namespaces as the folder names. If there is a manifest that does not follow the following convention i.e., below, landscaper will ignore that.

```bash
manifests
    /<namespace-name>/<manifest-name>.yaml
    /<namespace-name>/<manifest-name>.yaml
    /<namespace-name>/<manifest-name>.yaml
    /<namespace-name>/<manifest-name>.yaml
    /<namespace-name>/<manifest-name>.yaml
    /<namespace-name>/<manifest-name>.yaml
```

### Using the manifests
Helm must be installed before using these charts.
A user can use any namespace he/she would like to deploy to instead of stakater

**While using these charts below secrets should be present in the cluster**

1. Secret names
  - kubeconfig for external cluster
    - name: ```k8s-external-cluster-kubeconfig```
    - key: ```config```
  - Secret for username and password
    - name: ```chartmuseum-auth```
    - key: ```username```
    - key: ```password```
  - Webhook for slack notifications
    - name: ```slack-notification-hook```
    - key: ```channel```
    - key: ```webHookURL```
  - Github tokens for creating releases and generating repos
    - name: ```jenkins-hub-api-token```
    - key: ```hub```
  - Maven settings for jenkins
    - name: ```jenkins-maven-settings```
    - key: ```settings.xml```
  - Docker configuration for jenkins
    - name: ```jenkins-docker-cfg```
    - key: ```config.json```
  - Git ssh for jenkins
    - name: ```jenkins-git-ssh```
    - key: ```ssh-key```
    - key: ```ssh-key.pub```
  - Test configuration
    - name: ```ingress-monitor-controller-test-config```
    - key: ```config.yaml```
  - STK configuration
    - name: ```stk-config```
    - key: ```issuesAuth.yaml```
2. Claim names
  - Resource configuration
    - name: ```jenkins-mvn-local-repo```
