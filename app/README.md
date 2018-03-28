# app

This chart is an example of using a single shared chart to deploy generic applications.

I have sucessfully used a somewhat similar chart to deploy Ruby applications as part of a CD pipeline. There is however nothing in
here that limits this aproach to any partiular technology, something similar should be suitable for any tech stack that can
be Dockerised.

Please don't rely on this chart directly as it is my own personal playground for experimenting with this approach to using helm.

If this aproach is attractive to you feel free to copy this chart into your own organisation.

This approach is most powerful where you are able to default as many properties as possible, while this is not possible in this generic example if you maintain your own "app" chart there are many opertunites for defaults and standardisation:

For example:

* Could you infer the image repository from the name of the application?
* Do you use a framework like Rails that runs its webserver on a particular port?
* Can you add additional componennt kinds specilised to the types of application that you run.

## Usage

If you try to install this chart without first providing some configuration it will do very little.

In order for this to be useful we expect the user/operator to provide some confuration that describes the application to be deployed.

Helm will allow you to pass in multiple config files with the `-f` flag, and single variables with the `--set` flag.

The configuration is designed to be highly mergable, so you could keep a skeleton configuration checked into your application VCS
and then override some values for different environment provide secret variables etc.

## Example Config

Here is a minimal example config

```
# The name of the application
name: example-application
image:
  repository: alpine
  tag: 3.7

# A list of environment variables
env:
  HELLO:
    value: "Hello World"

  # secret vars are stored in a Kubernetes Secret
  PASSWORD:
    value: marmalade
    secret: true

components:
  hello:
    kind: CronJob
    schedule: */5 * * * *
    args: ["echo", "$HELLO"]
```

## Components

This chart models your application as a list of components that should be running

I have implimented 4 component "kinds", you should impliment your own specilised to the types of application you run.

### Deployment
Simply wraps the Kubernetes deployment resource.

Can be used for any sort of long running process.

Optionaly allows you to expose ports, and setup a matching service, and confiure probes.


### CronJob
Wraps the Kubernetes CronJob resource, use for batch jobs that should be triggered periodicly.

### HookJob
Kubernetes Job resource with the helm hook annotations set could be used for setting up your application or running database migrations, use with care as Helm does not manage hook resources directly.

## Ingress
Use to configure an ingress controller.
