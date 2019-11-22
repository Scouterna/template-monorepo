# Template for a repository with multiple services

This template is for a single repository with multiple services that are built
and deployed more or less independently. We use [Azure devops
pipelines](https://azure.microsoft.com/en-us/services/devops/pipelines/) for
build and deploy automation, and Kubernetes for hosting. Most boilerplate
configuration is done and taken care of in this repository, so you should be up
to speed with creating and running your thing quite quickly.

## What's in the package?

The goal is to have a package with everything needed to get your code running
(except of course for your actual program, that you'll have to provide
yourself). 

If you need help with any of these steps, please reach out on [MS
Teams](https://teams.microsoft.com/_#/conversations/General?threadId=19:cc9dfac625e042ff9cf55fad696208b3@thread.skype&ctx=channel).

### Build automation

There are configuration files for automated builds of Docker containers, that
should require almost no additional configuration. All services that are
included in `azure-pipelines.yaml` will be built, published to our internal
Docker registry at `socuterna.azurecr.io` and make kubernetes specifications
available for a release pipeline. The only requirement is that your service has
a `Dockerfile` that describe how it should be built.

### Deployment and hosting

The build pipeline contains no automated deployment, however the pipeline in the
standard `template` has some building blocks for simple deploying to our
Kubernetes environment:

* The `.k8s` directory contains templates for a Kubernetes Deployment and
	Service, the usual building blocks for a service in our cluster.
* The `build.yaml` template is set up to build a Docker container using your
	Dockerfile, publish the container to our registry at `scouterna.azurecr.io`,
	replace the image tag in the Kubernetes deployment speification and, finally,
	publish the `.k8s` directory as a build artifact.

The build artifact can easily be used in a Release Pipeline on [Azure
Pipelines](https://dev.azure.com/scouterna), to easily deploy the
right version of your service to our cluster.


## Adding a service

To add a new service, you'll need a few steps:



## Contributing to the template

The template is a constantly ongoing work as we learn and improve how we use our
tools. The current state is useable, but there are some known areas of
improvement:

### Build pipelines

The build pipelines still require a little bit too much configuration by
default. The goal is that it's enough to add a directory and a reference to that
service in one place, and it's all good to go as long as the service can use
a default Docker build process.

Currently there's no relase/deploy automation built into the template, this is
because the Azure tooling doesn't support configuring those outside the web
interface. Or, well, there is a way to add "deploy jobs" to the pipelines
configured with files in the repository, but those deploy jobs are far less
powerful and structured than the ones added as "release pipelines" in the web
interface. So for now we stick to releasing with the web tool.

### Kubernetes specifications

This isn't specifically about the template - our entire Kubernetes configuration
could benefit from some love. Some possible improvements:

* Start using [Helm](https://helm.sh) instead of our homebrew templating
	solution based on `sed`.
* Use more security features (network policies, security contexts)

