## OBJECTIVES

- Work with Red Hat OpenShift
- Deploy multi-container applications
- Work with container images
- Troubleshoot application deployment issues
- Work with image streams
- Work with configuration maps and secrets
- Work with the source-to-image (S2I) framework
- Work with templates
- Work with OpenShift Pipelines

### Typing aids
1. Bash Enviromnent variables:
```bash
vim ~/.bashrc
alias oclogin='oc login ******'
alias podmanlogin="podman login *****'
```
2. VIM config
```bash
vim ~/.vimrc 
syntax on
set nu
set expandtab
set tabstop=2
set shiftwidth=2
```
<br>
<br>

### PRACTICE 1: Fix build issues

EXAMPLE 1
  - repo: https://github.com/ozyohthree/DO288-apps 
  - branch name:practice1
  - folder name: nodejs-helloworld
  - project name: hello-world
  - application name: hello

    <br>
    <details>
    <summary>CheatSheet</summary>

    ```bash
    # deploy
    oc new-app --name=hello https://github.com/ozyohthree/DO288-apps#practice1 --context-dir=nodejs-helloworld

    # check logs
    oc logs -f bc/hello

    # check issue
    python -m json.tool package.json

    # rebuild after fixing and checking in new package.json fine
    oc start-build bc/hello

    # test 
    curl -s https://hello-hello-world*  
    ```

    </details>
    <br>
    <br>

EXAMPLE 2
  - repo: https://github.com/ozyohthree/DO288-apps 
  - branch name:source-build
  - folder name: nodejs-helloworld
  - project name: greet-proj
  - application name: greet
  - Nodejs image: nodejs:16-ubi8
  - Npm modules registry: http://nexus-infra.apps.ocp4.example.com/repository/npm (use npm_config_registry as the build-env key)

    <br>
    <details>
    <summary>CheatSheet</summary>

    ```bash
    oc new-app --name=hello nodejs:16-ubi8~https://github.com/ozyohthree/DO288-apps#source-build \
      --build-env npm_config_registry=http://nexus-infra.apps.ocp4.example.com/repository/npm \
      --context-dir=nodejs-helloword

    # check logs
    oc logs -f bc/hello

    # check issue
    python -m json.tool package.json

    # rebuild after fixing and checking in new package.json fine
    oc start-build bc/hello

    # test
    curl -s https://hello-hello-world*  
    ```

    </details>
    <br>
    <br>

### PRACTICE 2: Deploy Simple applications
- EXAMPLE 1: Deploying Applications by Using the oc and odo CLIs
  1. Deploy an application using the new-app command
  registry:  registry.ocp4.example.com:8443/redhattraining/openshift-dev-deploy-cli-weather:1.0
  expose the app outside of the cluster
  name the route resource "weather"
      - tip: oc new-app

  2. Deploy the same applictation using odo
  use the devfile located in ~/DO288/labs/deploy-cli/weather
  Deploy the application in the odo-deploy-cli project
      - tip: odo deploy

- EXAMPLE 2: Deploy an application that uses a database
  1. Deploy an internal Postgresql database using the database template in the web console
      - Project name: deploy-review
      - Database Service Name: postgresql
      - PostgreSQL Connection Username: developer
      - PostgreSQL Connection Password: test
      - PostgreSQL Database Name: todo_list

  2. Use the oc command to deploy the application's container image and create a route
      - Application Name: todo-list
      - Application Image: registry.ocp4.example.com:8443/redhattraining/openshift-dev-deploy-review-todo-list

      - Test application and debug root cause of failure

      - Redeploy application
      - Use the oc command to delete all the application objects (tip: use -l app=todo-list)
      - Use the oc command to deploy the application by adding the DB_PASSWORD=test environmental variable 
      - create the applications's route and test

  <br>
  <details>
  <summary>CheatSheet</summary>

   ```bash
  oc new-app --name=todo-list registry.ocp4.example.com:8443/redhattraining/openshift-dev-deploy-review-todo-list \
  --env DB_PASSWORD=test
 
  # Add data
  curl -i -H "Content-Type: application/json" \
  http://todo-list-deploy-review.apps.ocp4.example.com/todos \
  --data '{ "task": "do laundry" }'

  # retrieve data
  curl -s \
  http://todo-list-deploy-review.apps.ocp4.example.com/todos; echo
  ```

  </details>
  <br>
  <br>



### PRACTICE 4: Build and Deploy using Docker Strategy

- Git repo: https://github.com/ozyohthree/spring-petclinic.git
- application name: pets
- Strategy: Dockerfile
- Should be able to redeploy with 'oc start-build'

  <br>
  <details>
  <summary>CheatSheet</summary>

   ```bash
   # build
  oc new-build https://github.com/ozyohthree/spring-petclinic.git --strategy=docker
  
  # check logs 
  oc logs -f bc/spring-petclinic

  # copy image url from logs to deploy
  oc new-app image-registry.openshift-image-registry.svc:5000/spring-petclinic@sha256:thehashvalue

  # expose the app
  oc expose svc spring-petclinic

  # get route and view app in browser

  ```

  </details>
  <br>
  <br>


### PRACTICE 5: HELM with TLS Security
1. HELM
- Chart name: exochart
- Chart image: quay.io/redhattraining/exoplanets:v1.0
- Use the cockroachdb Helm Chart with version 6.0.4 from the repository https://charts.cockroachdb.com/ as the database dependency.
- port: 8080
- Use TLS security
- DB Variables
    - Variable	Value
    - DB_HOST	exoplanets-cockroachdb
    - DB_PORT	26257
    - DB_USER	root
    - DB_NAME	postgres
- project name: planets
- chart name: exoplanets
- Add values file for test environment
```bash 
Env   Pods	Memory    CPU Limit
Test  5	    128Mi     250m

Add label
Environment: test
```


2. Kustomize it
- Kustomize Directory: exokustom
- Use helm template to create base deployment resource
- use test directory for Test Overlay
```bash
Env   Pods	Memory    CPU Limit
Test  5	    128Mi     250m
```
- Deploy an application with Helm chart
- use a separate values file
- update a environment specific label via the values file

  <br>
  <br>

### PRACTICE 6: TEMPLATES
- Deploy uisng templates an application with UI and backend componets
- each with a 1 template for building and 1 for deploying 
https://docs.openshift.com/container-platform/4.12/openshift_images/using-templates.html#using-templates
- Create application with 
  - template: openjdk18-web-basic-s2i
  - APPLICATION_NAME=spring-rest
  - SOURCE_REPOSITORY_URL=https://github.com/redhat-cop/spring-rest.git 
  - CONTEXT_DIR=''

  <br>
  <details>
  <summary>CheatSheet</summary>

   ```bash
  oc new-app --template=openjdk18-web-basic-s2i -p APPLICATION_NAME=spring-rest -p SOURCE_REPOSITORY_URL=https://github.com/redhat-cop/spring-rest.git -p CONTEXT_DIR=''
  ```

  </details>
  <br>
  <br>


### PRACTICE 7: Triggers
Update tekton pipeline
  - EventListener
  - Trigger Template
  - Trigger Binding
  - Trigger
  - https://github.com/ozyohthree/pipelines-tutorial
