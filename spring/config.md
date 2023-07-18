- problem with embeded config any change in config file will require
  <br> building, testing, and deploying the application again.

- in microservice architecture, some configuration is common across
  <br> multiple services. for example, the database connection information in testing environment. 
    - if we redundantly configure the same information in each service,
    <br> we will have to change it in multiple places when it changes.

<hr>
<br>

# use cases for external configuration
- Database config
- feature toggles configuration
    - under development features are enabled in testing environment, but disabled in production environment.
        - if we have different configuration files for each environment, that's will be a useful
        pattern to avoid long running feature branches.
- Thread Configuration
    - u can exprement with different thread pool sizes in testing environment to get the best value for production.

- Lives separately to application code

  - it's not part of the application code, so it can be changed without rebuilding the application.

- microservice 
    - identify common-configurations and move them to a separate repository. 
    - u can override the common configuration in each service if needed.
    
<br>
<hr>

# Advantages of injecting configuration at Deployment time

- u can change the configuration without rebuilding the application.
- separate configuration from application code.
  - comfiguration is centralized in one place. instead of being scattered across multiple services.
- externalizing sensitive configuration from the code and the deployment artifact. which make them easier to manage.
  - for example, database password and API keys.

<hr>

# Spring Cloud Config

- config can be backed by a version control system such as
  - version control {git}.
  - file system{easier to modify but it's hard to mange if u go for horizontal scaling u'll need to replicate it on different host}
  - database
  - cloud storage {s3..}.

# config server
- Git Backend
    - native support for versioning and auditing.
    - distributed 
        -  each server can has its own copy. if git server goes down, the distributed copy in the<br>
        config server will act as a cache. with a worst case scenario configuration will be stale.
    - can be served over https and ssh
- u can force the config server to fetch the configuration from the git server on startup.
    - spring.cloud.config.server.git.force-pull=true
- it's also possible to configure the server to access the repo over ssh if the key is stored in the <br> neccessary location.
    


# preparing config client
- dependency
    - spring-cloud-starter-config
    - spring-retry
       - made the application fault-tolerant and retry multiple times in the event of 
       failing to fetch config from  the config server .
- set value to spring.application.name
    - this value will be used to fetch the configuration from the config server.
- spring.config.import: prefix optional
    - tell the app to continue to boot even if it fails to fetch the configuration from the config server.
    - in case of this config is mandatory{database-password}, u can remove the prefix optional. and add <br>
    spring.config.fail-fast=true
        - the client will try and connect to the config server several times before eventually<br>
        backing off and failing. 
        - u can configure rety configs in application.properties
            - spring.cloud.config.retry.initial-interval=1000
            - spring.cloud.config.retry.max-attempts=10
            - spring.cloud.config.retry.max-interval=2000
            - spring.cloud.config.retry.multiplier=1.1
    - u can choose the branch to retrieve configs from by setting spring.cloud.config.label=branchName
    - if u activate multiple profiles, the last value will override the previous one.

# debugging spring environment
- u can use spring actuator to view the environment variables and from where they are loaded.
    - /actuator/env

<hr>

- to inject configs to all services u can set them at application.yaml
  - u can specify configs for a specific profile to all services by adding the profile name to the file name.
    - application-dev.yaml
    - application-prod.yaml
    - application-test.yaml









    
