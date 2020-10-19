# Using Templates

A `Template` is an OpenShift object that describes a set of objects that can be parameterized and processed. Using the CLI, you can process a file defining a template to return the list of objects to standard output.

## Using a Template 

1. Create a new Template file `template.yaml`,

    ```console
    cat > template.yaml <<EOF
    apiVersion: v1
    kind: Template
    metadata:
      name: spring-boot-app
      namespace: ${NAMESPACE_NAME}
      annotations:
        description: "Spring Boot App"
        tags: spring-boot, java
    parameters:
    - name: NAMESPACE_NAME
      description: Name of the namespace
      required: true
      value: oc-docker-build
    - name: APPLICATION_NAME
      description: Name of the application image
      required: true
      value: spring-boot-app
    - name: APPLICATION_IMAGE
      description: Full location of the application image
      required: true
      value: quay.io/remkohdev/spring-boot-app
    - name: APPLICATION_PORT
      description: Port to access the application image
      required: true
      value: "8080"
    labels:
      app: ${APPLICATION_NAME}
    objects:
    - apiVersion: apps.openshift.io/v1
      kind: DeploymentConfig
      metadata:
        name: ${APPLICATION_NAME}
        namespace: ${NAMESPACE_NAME}
        labels:
          app: ${APPLICATION_NAME}
      spec:
        replicas: 3
        selector:
          app: ${APPLICATION_NAME}
        strategy:
          type: Rolling
        template:
          metadata:
            labels:
              app: ${APPLICATION_NAME}
          spec:
            containers:
            - image: ${APPLICATION_IMAGE}
              name: ${APPLICATION_NAME}
              imagePullPolicy: Always
              ports:
              - containerPort: ${APPLICATION_PORT}
                protocol: TCP
    - apiVersion: v1
      kind: Service
      metadata:
        labels:
          app: ${APPLICATION_NAME}
        name: ${APPLICATION_NAME}
        namespace: ${NAMESPACE_NAME}
      spec:
        ports:
        - name: tcp-port
          protocol: TCP
          port: ${APPLICATION_PORT}
          targetPort: ${APPLICATION_PORT}
        selector:
          app: ${APPLICATION_NAME}
          deploymentconfig: ${APPLICATION_NAME}
        type: ClusterIP
    - apiVersion: route.openshift.io/v1
      kind: Route
      metadata:
        labels:
          app: ${APPLICATION_NAME}
        name: ${APPLICATION_NAME}
        namespace: ${NAMESPACE_NAME}
      spec:
        port:
          targetPort: tcp-port
        to:
          kind: Service
          name: ${APPLICATION_NAME}
          weight: 100
        wildcardPolicy: None
    EOF
    ```

2. Process the template and pipe the list of objects to create in the active OpenShift project,

    ```
    oc process -f template.yaml  | oc create -f -
    ```

3. The following objects were created,

    ```
    % oc get all
    NAME    READY    STATUS    RESTARTS    AGE
    pod/spring-boot-app-1-7qvxd    1/1    Running    0    17m
    pod/spring-boot-app-1-8rxkp    1/1    Running    0    17m
    pod/spring-boot-app-1-deploy   0/1    Completed    0    17m
    pod/spring-boot-app-1-pgsk7    1/1    Running    0    17m

    NAME    DESIRED    CURRENT    READY    AGE
    replicationcontroller/spring-boot-app-1    3    3    3    17m

    NAME    TYPE    CLUSTER-IP    EXTERNAL-IP    PORT(S)    AGE
    service/spring-boot-app    ClusterIP    172.21.191.199    <none>    8080/TCP    17m

    NAME    REVISION    DESIRED    CURRENT    TRIGGERED BY
    deploymentconfig.apps.openshift.io/spring-boot-app    1    3    3    config

    NAME    HOST/PORT    PATH    SERVICES    PORT    TERMINATION    WILDCARD
    route.route.openshift.io/spring-boot-app    spring-boot-app-oc-docker-build.remkohdev-roks44-3n-clu-2bef1f4b4097001da9502000c44fc2b2-0000.us-south.containers.appdomain.cloud    spring-boot-app   8080-tcp    None
    ```

4. Because I am creating the application from an existing image, no `BuildConfig` was defined.

### Create an Application from Template

1. Create and store the application template,

    ```
    oc create -f template.yaml
    ```

1. List the stored templates

    ```console
    oc get templates
    NAME    DESCRIPTION    PARAMETERS    OBJECTS
    spring-boot-app    Spring Boot App    4 (all set)    3
    ```

2. Create the application from the stored template,

    ```
    oc new-app spring-boot-app --build-env strategy.dockerStrategy.dockerfilePath=./Dockerfile1
    ```
