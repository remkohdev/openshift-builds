# OpenShift Builds

## About this Workshop

A `Build` is the process to transform source code into a runnable image. A `BuildConfig` object is the definition of the entire build process. 

There are three primary [Build strategies](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html) in OpenShift Container Platform (OCP) 4.x:

* Docker build,
* Source-to-Image (S2I) build,
* Custom build.

The Pipeline build strategy using Jenkins is deprecated in OpenShift Container Platform (OCP) 4.x. The Jenkins pipeline is still available via the Jenkins operator. Equivalent and improved functionality is present in the [OpenShift Pipelines based on Tekton](https://ibm.github.io/tekton-tutorial-openshift/).

## Agenda

|   |   |
| - | - |
| [Setup](setup/README.md) | Pre-requirements for the workshop. |
| [Docker build](docker-build/README.md) | Use Docker build strategy. |
| [Source-to-Image (S2I)](https://ibm.github.io/s2i-open-liberty-workshop/) | Use Source-to-Image (S2I) build strategy. |
| [Pipeline build](pipeline-build/lab-01/README.md) | Use a Jenkins Pipeline and OpenShift Pipeline build strategy. |
| [Tekton Deployment](https://ibm.github.io/tekton-tutorial-openshift/) | Use Tekton to Deploy to OpenShift. See also [Tekton Resources](tekton-build/README.md) |

## Compatibility

This workshop has been tested on the following platforms:

* **OpenShift**: version 4.4
* Client:
  * **linux**: version 4.15.0
  * **OpenShift CLI**: version 4.5.0
  * **git**: version 2.17.1
  * **Apache Maven**: version 3.6.0
  * **Java version**: version 11.0.6
  * **Docker CE**: version 19.03.6
  * **curl**: version 7.58.0

## Credits

* [Oliver Rodriguez](https://github.com/odrodrig)
* [Remko de Knikker](https://github.com/remkohdev)