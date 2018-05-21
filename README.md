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
````
**While using these charts below secrets should be present in the cluster**

1. k8s-external-cluster-kubeconfig

2. chartmuseum-auth

3. slack-notification-hook

4. jenkins-hub-api-token

5. jenkins
 - client.secret

6. jenkins-docker-cfg
 - config.json

7. jenkins-git-ssh
 - ssh-key.pub

8. jenkins-hub-api-token
 - hub

9. jenkins-maven-settings
 - settings.xml

10. jenkins-recommender-api-token
 - token

11. jenkins-ssh-config
 - config

12. jenkins-release-gpg
 - trustdb.gpg
 - pubring.gpg
 - gpg.conf
 - secring.gpg

13. k8s-external-cluster-kubeconfig
 - config

14. k8s-current-cluster-kubeconfig
 - config

15. ingress-monitor-controller-test-config
 - config.yaml

16. chartmuseum-auth
 - username
 - password

17. slack-notification-hook
 - channel
 - webHookURL
