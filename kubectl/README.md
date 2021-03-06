# Kubectl

Provides access to `kubectl`, the command line cli from Kubernetes to manage clusters.

The plugin may also be used together with `terraform`, `oc`, `helm` and `az`. In this case all the plugins
will share the same Kubernetes context configuration through volume mappings. This allows, for example, to
setup the context configuration through `azLogin` and `ocLogin` tasks next to the built-in  `KubectlUseContext` task
(see below).


## Setup

Add this library to the classpath:

```
buildscript {
	dependencies {
	    ...
		classpath 'gradle.plugin.com.github.rmee:kubernetes:<VERSION>'
	}
}
```


## Example

```
kubectl{
	url = ...
	credentials{
		userName = ...
		password = ..
		token = ... // (alternative to userName/password)
   }
   namespace = 'default'
   insecureSkipTlsVerify = false
   cli {
     version = '2.9.1'
     imageName = 'dtzar/helm-kubectl'
   }
}

```

For more detailed information, have a look at the `KubectlExtension`.

For an example app have a look at https://github.com/crnk-project/crnk-example[crnk-example] how to deploy
to Google Cloud with Kubernetes and Helm. Or https://github.com/cord3c/cord3c-project/tree/master/cord3c-example-deployment[cord3c-example]
for how to deploy to DigitalOcean.


## Tasks

- `KubectlExec` allow to issue a command. Alternatively, `kubectl.exec(...)` can be used.
- `KubectlUseContext` to connect to a given cluster, namespace with username/password or a token. The task
  works in two flavors. A `contextId` can be passed to make use of an existing context configuration. But `contextId`
  can also be left empty, then `url`, `userName`, `password`, `token` and `namespace` will be used to
  setup a new current context. The second behavior closely matches `ocLogin` and `azLogin` to perform simple logins.

## Wrapper

A wrapper `kubectl` will be generated into the project root to allow easy access from the command line next to Gradle tasks:

```
#!/usr/bin/env sh
exec docker run -v build\.kube:/build/home/.kube/ dtzar/helm-kubectl:3.2.1 kubectl "$@"
```