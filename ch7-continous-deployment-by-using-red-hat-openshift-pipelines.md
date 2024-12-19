[Work with Openshift Pipelines](https://role.rhu.redhat.com/rol-rhu/app/courses/do288-4.12/pages/ch07) 
- [Example Task 1: Create CI/CD Workflow for a vertx application - Lab Chapter 7 Section 7.5](https://role.rhu.redhat.com/rol-rhu/app/courses/do288-4.12/pages/ch07s05) 
    - Application Git repository: https://github.com/ozyohthree/DO288-apps/tree/pipelines-review/apps/pipelines-review/vertx-site
    - Pipelines Git Repository: https://github.com/ozyohthree/DO288-apps/tree/pipelines-review/solutions/pipelines-review
    - Update the maven task and pipeline per the requirements.
    - NOTE: use skopeo-copy (skopeo-copy-internal not available) which requires a secret to the pipeline serviceaccount to push to the registry (see spec using tkn)
    - Create project: pipelines-review
    - Deploy tasks and pipeline.
    - run the pipeline
    - Validate Application is running.
    ```bash
    curl -s vertx-site-pipelines-review.apps.ocp4.example.com; echo
    ```
    - Update the Pipeline Trigger, TriggerTemplate, TriggerBinding and EventListener for the application.
    - Update application to version 2.0.
    - Simulate webhook trigger to run a pipeline build:
    ```bash
    curl -H 'Content-Type: application/json'                        \
         -d '{                                                      
              "head_commit": {                                      
                "id": "pipelines-review"                            
              },                                                    
              "repository": {                                       
                "name": "apps/pipelines-review/vertx-site",         
                "url": "https://github.com/ozyohthree/DO288-apps"   
              }                                                     
            }'                                                      \
        -X POST http://el-maven-java-pipeline-pipelines-review.apps.ocp4.example.com
    ```
    - Validate application:
    ```bash
    curl -s vertx-site-pipelines-review.apps.ocp4.example.com; echo
    ```

- [Example Task 2:: Based on Openshift pipelines Tutorial](https://github.com/openshift/pipelines-tutorial) 
    - This task involved building a Tekton pipeline for a Microservices Architected application called vote with 2 components, UI and API.
    - Task Git repositories
        - Pipelines repository: https://github.com/ozyohthree/pipelines-tutorial  
        - UI Application repos (Main): https://github.com/ozyohthree/pipelines-vote-ui
        - API Application repo (Main): https://github.com/ozyohthree/pipelines-vote-api
    - Git Branches (all 3): do288-workgroup
    - Application name: vote 
    - Task 1: Deploy and Run Tekton pipeline and verify successful build and application deployment. 
        - TODO: application validation success criteria
    - Task 2: Complete and deploy Triggers (TriggerTemplate and TriggerBinding)
    - Task 3: Trigger a pipeline build by updating and checking in code to repository. Validate successful build and application deployment
    - TODO: application validation success criteria