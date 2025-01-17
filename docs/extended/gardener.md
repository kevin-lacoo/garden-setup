# Advanced Configuration Options for 'landscape.gardener'

### Seed Candidate Determination Strategy
`landscape.gardener.seedCandidateDeterminationStrategy` has two possible values: `SameRegion` (default) and `MinimalDistance`. In the first case, shoots can only be created in regions where a seed exists and only those regions will show up in the dashboard. In the latter case, shoots can be created in any region listed in the cloudprofile and the geographically closest seed will be used.


### Network Policies
If `landscape.gardener.network-policies.active` is set to `true`, garden-setup will deploy network policies into the `garden` namespace. Currently, these are only egress rules for the 'virtual' kube-apiserver and the Gardener dashboard, other components or incoming traffic are not affected for now.

The default for `landscape.gardener.network-policies.active` is `false`, because the network policies have been shown to cause problems in some environments. It is planned to enable the policies by default, though, once the problems have been solved.

### Extensions

The `landscape.gardener.extensions` is optional.

#### Activating/Deactivating Extensions

While garden-setup automatically decides which Gardener extensions to deploy, it is possible to overwrite this decision manually. By setting `landscape.gardener.<extension_name>.active` to `true`, the extension will be deployed, independently of whether it is needed or not. Similarly, setting the flag to `false` will deactivate the extension and it won't be deployed. You should handle the latter one with care, as most extensions which are deployed by default are needed and deactivating them will result in a broken Gardener landscape.

#### valueOverwrites for Extensions

Whatever is specified in `landscape.gardener.extensions.<extensionName>.valueOverwrites` will be given directly to the helm values for the extension, so you can overwrite the corresponding default values. 
Some values are set by default (take a look in the [deployment.yaml](../../components/gardener/extensions/deployment.yaml)), this values can also overwrite with the `landscape.gardener.extensions.<extensionName>.valueOverwrites`.

The following extensions are available in `landscape.gardener.extensions`:

- dns-external: [values.yaml](https://github.com/gardener/gardener-extension-shoot-dns-service/blob/master/charts/gardener-extension-shoot-dns-service/values.yaml)
- os-ubuntu: [values.yaml](https://github.com/gardener/gardener-extension-os-ubuntu/blob/master/charts/gardener-extension-os-ubuntu/values.yaml)
- os-gardenlinux: [values.yaml](https://github.com/gardener/gardener-extension-os-gardenlinux/blob/master/charts/gardener-extension-os-gardenlinux/values.yaml)
- os-suse-chost: [values.yaml](https://github.com/gardener/gardener-extension-os-suse-chost/blob/master/charts/gardener-extension-os-suse-chost/values.yaml)
- os-coreos: [values.yaml](https://github.com/gardener/gardener-extension-os-coreos/blob/master/charts/gardener-extension-os-coreos/values.yaml)
- provider-aws: [values.yaml](https://github.com/gardener/gardener-extension-provider-aws/blob/master/charts/gardener-extension-provider-aws/values.yaml)
- provider-gcp: [values.yaml](https://github.com/gardener/gardener-extension-provider-gcp/blob/master/charts/gardener-extension-provider-gcp/values.yaml)
- provider-azure: [values.yaml](https://github.com/gardener/gardener-extension-provider-azure/blob/master/charts/gardener-extension-provider-azure/values.yaml)
- provider-openstack: [values.yaml](https://github.com/gardener/gardener-extension-provider-openstack/blob/master/charts/gardener-extension-provider-openstack/values.yaml)
- networking-calico: [values.yaml](https://github.com/gardener/gardener-extension-networking-calico/blob/master/charts/gardener-extension-networking-calico/values.yaml)
- shoot-cert-service: [values.yaml](https://github.com/gardener/gardener-extension-shoot-cert-service/blob/master/charts/gardener-extension-shoot-cert-service/values.yaml)

Example to set the `imageVectorOverwrite` (see [values.yaml](https://github.com/gardener/gardener-extension-networking-calico/blob/master/charts/gardener-extension-networking-calico/values.yaml#L14-L26)) for `gardener-extension-networking-calico`:

```yaml
  ...
  gardener:
    ...
    extensions:
      networking-calico:
        valueOverwrites:
          imageVectorOverwrite: |
            images:
            - name: calico-node
              sourceRepository: github.com/projectcalico/calico
              repository: some-other-registry/calico/node
            - name: calico-cni
              sourceRepository: github.com/projectcalico/cni-plugin
              repository: some-other-registry/calico/cni
            - name: calico-typha
              sourceRepository: github.com/projectcalico/typha
              repository: some-other-registry/calico/typha
            ...
```

#### Change default shoot prefix domain
Per default all shoot api server domains are something like '<clustername>.<project>.shoot.<basedomain>'. If you want to change the 'shoot' term you can use `landscape.gardener.shootDomainPrefix` and set a value you like.  


### Resources

It is possible to overwrite the default values for the resource requests/limits of the Gardener components by setting the corresponding values in `landscape.gardener.resources`.
```yaml
  ...
  gardener:
    ...
    resources:
      apiserver:
        limits:
          cpu: 300m
          memory: 256Mi
        requests:
          cpu: 100m
          memory: 100Mi
      admission: <see above>
      controller: <see above>
      scheduler: <see above>
```
All fields are optional.