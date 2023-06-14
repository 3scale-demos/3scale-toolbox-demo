# 3scale Toolbox Demo
A demonstration of various capabilities of the 3scale toolbox. This is not a comprehensive demonstration of everything, but rather a selection of its capabilities.

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
$ DEST="https://f77d455b4d97f558a0392fe404119eb7d0e2be2c123a851c822861b55af299c0@3scale-admin.apps-crc.testing"
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

## Next Steps

1. [Creating of an API](creation.md)
1. [Inspection of APIs](inspection.md)
1. [Managing an API](management.md)
