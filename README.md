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
