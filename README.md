# apigee-config-maven-plugin

Maven plugin to create Apigee config like Cache, Target Server, Developers, Developer Apps that can be used alongwith the apigee-deploy-maven-plugin to deploy a working API in one go.

A new config file edge.json is used to hold all the config data that will create the corresponding entities in Apigee Edge.

The org and env information comes from the maven profile and helps deploy an API and the associated config to any environment just by using the right profile name in the command line.

## Plugin Build
Maven plugin needs to be built locally before use. This is required till this plugin is made available in the central maven repo.

To build,
```
# clone repo
cd apigee-config-maven-plugin
mvn clean install
```
This builds and installs the plugin for local use.

## Plugin Usage
```
mvn install -Ptest -Dusername=${APIGEE_USER} -Dpassword=${APIGEE_PASS} -Dapigee.config.options=create

  # Options

  -P<profile>
    used to pick a profile in the parent pom.xml (shared-pom.xml in the example)
  

  -Dapigee.config.options
    overwrite settings
    none   - default. No action
    create - create when not found
    update - update when found; create when not found
```
The default action is no-action and it helps deploy APIs without affecting config.

## Sample project
Refer to an example API project here
```
/samples/src/edge/HelloWorld
```

To use
  - Build the maven plugin in the same machine (steps above)
  - edit samples/src/edge/shared-pom.xml, and update org and env to point to your Apigee org, env. You can create several profiles corresponding to each env in your org(s).
    ```
      <apigee.org>myorg</apigee.org>
      <apigee.env>test</apigee.env>
    ```
  - set Edge credentials and run mvn
    ```
    cd /samples/src/edge/HelloWorld
    export APIGEE_USER <your-apigee-username>
    export APIGEE_PASS <your-apigee-password>
    mvn install -Ptest -Dusername=${APIGEE_USER} -Dpassword=${APIGEE_PASS} -Dapigee.config.options=create
    ```

The example project performs the following steps in sequence. This sequence is
inherent to the platform and is managed using the sequencing of goals in pom.xml
  - Creates Caches
  - Creates Target servers
  - Deploy API
  - Creates API products
  - Creates Developers
  - Creates Developer Apps

## edge.json - v1.0
edge.json contains all config entities to be created in Apigee Edge. It is organized into 3 scopes corresponding to the scopes of config entities that can be created in Edge.
   ```
     envConfig
     orgConfig
     apiConfig
   ```
For example, API product is an orgConfig whereas Cache is an envConfig. The envConfig is further organized into individual environments as follows.
   ```
     envConfig.test
     envConfig.dev
     envConfig.pre-prod
   ```
The environment name is intended to be the same as the profile name in pom.xml (parent-pom.xml or shared-pom.xml).

The final level is the config entities. 
   ```
     orgConfig.apiProducts[]
     orgConfig.developers[]
     orgConfig.developerApps[]
     envConfig.test.caches[]
     envConfig.test.targetServers[]
   ```

Each config entity is the payload of the corresponding management API. The example project may not show all the attributes of config entities, but any valid input of a management API would work.

Config entities like "developerApps" are grouped under the developerId (email) they belong to.

## TODO - ideas welcome
  - KVM (encrypted data support)
  - Vault (encrypted data support)
  - Keystore, Truststore support
  - Virtual host (on-prem)
  - Custom roles
  - User role association