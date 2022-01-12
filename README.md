# 3scale-toolbox-demo
A demonstration of various capabilities of the 3scale toolbox

## Prerequisites

See the 3scale toolbox image in the Red Hat Ecosystem Catalog. You must have a Red Hat registry service account. The examples in this topic assume that you have Podman installed.

### Install Procedure

Log in to the Red Hat Ecosystem Catalog:
~~~
$ podman login registry.redhat.io
Username: ${REGISTRY-SERVICE-ACCOUNT-USERNAME}
Password: ${REGISTRY-SERVICE-ACCOUNT-PASSWORD}
Login Succeeded!
~~~
Pull the toolbox container image:
~~~
$ podman pull registry.redhat.io/3scale-amp2/toolbox-rhel8:3scale2.11
~~~
Verify the installation:
~~~
$ podman run registry.redhat.io/3scale-amp2/toolbox-rhel8:3scale2.11 3scale help
~~~

### Environment Setup
Create a personal access token and set an environment variable with the format of `https://{token}@{server}` for ease of use with the demo

Your token will be different.
~~~
$ DEST="https://7dd86493b9ab66329635ae48198c0adb75025a50fddfaeb5f0b6e3fb86407876@3scale-admin.apps-crc.testing"
~~~

For shorthand, create an alias that contains the needed podman options for ease of use during the demo.
~~~
$ alias toolbox='podman run -u root -v $PWD:/tmp:Z -w=/tmp registry.redhat.io/3scale-amp2/toolbox-rhel8:3scale2.11'
~~~
This will mount the current user director as /tmp inside the container and set the container user's working directory to /tmp. The :Z flag tells podman to relable the volume's content to match the label inside the container, which is required to satisfy SELinux.

You should now be able to verify your setup with these commands:

~~~
$ toolbox 3scale help
$ toolbox ls -al
~~~

# Demonstrations

## Import complete service

The import command will create a new complete service based on the OpenAPI Spec json file that is stored under version control: [alerts-spec.json](alerts-spec.json)

~~~
$ toolbox 3scale import openapi -d $DEST alerts-spec.json --default-credentials-userkey=dummykey -k
~~~
The output provides the service id, which will be needed to manipulate the service later.
~~~
Created service id: 4, name: Weather Alerts
Service proxy updated
destroying all mapping rules
Created GET /alerts/active$ endpoint
Service policies updated
~~~

If the API (under version control) is ever updated, this same command will recreate the service with the new definitions provided the name of the API in the spec file has not changed.

## list methods
Importing from an OAS file will automatically create the defined methods and mapping rules. It is possible to view and manipulate them.