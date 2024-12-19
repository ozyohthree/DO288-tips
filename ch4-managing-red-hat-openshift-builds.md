- [Task 1: Customize S2I build by overriding the default scripts](https://role.rhu.redhat.com/rol-rhu/app/courses/do288-4.12/pages/ch04s08) 
    - Objective: Customize S2I *assemble* and *run* scripts
    - Customize the build scripts then and deploy an application called "greeting" using the *latest* tagged rhscl/httpd-24-rhel7 build image using the following requirements:
        - Add an info.html page that contains the below build message.
            - "Page Built On MMM dd, YYYY at HH:MM"
            - "Proudly served by Apache HTTP Server version $HTTPD_VERSION"
    - Git repository: https://github.com/ozyohthree/DO288-apps.git
    - Git branch: source-build
    - Git directory: s2i-scripts
    - Project name: greeting
    - Application name: greeting 
    - Make sure the deployment is using the latest rhscl/httpd-24-rhel7 image
    - validate:
    ```bash
    $ curl -s http://greeting-greeting.apps.ocp4.example.com

    $ curl -s http://greeting-greeting.apps.ocp4.example.com/info.html
    ```
    FYI:
    - You can include environment variables to your build environment by including an [environment file](https://docs.openshift.com/container-platform/4.12/cicd/builds/build-strategies.html#builds-strategy-s2i-build_build-strategies) in *.s2i* folder

    <br>
    <details>
    <summary>CheatSheet</summary>

    ```bash
    # find the latest image
    $ oc describe is httpd -n openshift

    # explore the s2i scripts
    $ podman run --name webserver -it  --rm \
    registry.ocp4.example.com:8443/ubi9/httpd-24 bash

    bash-5.1$ cd /usr/libexec/s2i/

    # assemble script
    cp -Rf /tmp/src/*.html ./

    DATE=`date "+%b %d, %Y @ %H:%M"`

    echo "Page built on $DATE <br>" >> ./info.html
    echo "Proudly served by Apache HTTP Server version $HTTPD_VERSION" >> ./info.html

    # deploy and run application
    $ oc new-app \
         --name greeting \
         --context-dir s2i-scripts \
         registry.ocp4.example.com:8443/ubi9/httpd-24~https://github.com/ozyohthree/DO288-apps.git#source-build
    ```

    </details>

    <br>
    <br>

- [Task 2: Deploy application with Docker Strategy and fix Issue with OpenShift Build](https://role.rhu.redhat.com/rol-rhu/app/courses/do288-4.12/pages/ch04s09)
    - Objectives: 
        - Create, Start, Rebuild Application Build
        - Debug and fix failed build
    - Git repository: https://github.com/ozyohthree/DO288-apps.git
    - Git branch: builds-review
    - Git directory: builds-review/apps/expense-service    
    - Project Name: expense
    - Application name: expense-service
    - *Build Strategy: Docker*
    - validate:
    ```bash
    curl -s \
      expense-service-expense.apps.ocp4.example.com/expenses | jq
    ```

    <details>
    <summary>CheatSheet</summary>

    ```bash
    # build and deploy application
    $ oc new-app \
       --name expense-service \
       --strategy Docker \
       --context-dir builds-review/apps/expense-service \
       https://github.com/ozyohthree/DO288-apps.git#builds-review

    # check logs
    $ oc logs deploy/expense-service

    # debug application
    $ oc debug deploy/expense-service

    # build application locally
    $ mvn -Dmaven.compiler.release=11 clean package

    # run application locally
    $ java -jar expense-service-1.0.0-SNAPSHOT-runner.jar

    # test application running locally
    curl -s http://0.0.0.0:8080/expenses | jq

    # test application running on OCP
    curl -s \
         expense-service-expense.apps.ocp4.example.com/expenses | jq




    ```

    </details>

    <br>
    <br>

- [Task 3: Fix s2i issues](https://role.rhu.redhat.com/rol-rhu/app/courses/do288-4.10/pages/ch04s09)
    - The application team has been having issues deploying the below application, troubleshoot and resolve the issue. 
    - Application Git repository: 
    - Application Git branch: builds-review
    - Git directory: build app
    - Application stack: Nodejs
    - Application Nexus server: http://<update this>/repository/nodejs
    - Build script: build.sh