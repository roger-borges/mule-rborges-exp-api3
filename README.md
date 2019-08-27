# rborges-exp-api3
## Purpose

This project is an Experience API...


## Uses

The project contains examples using:

* The minimal-logging connector, 
* The configuration property service,
* Maven deployment using the mule-maven-plugin descriptor,
* Automated Api Manager gateway auto-discovery registration with ApiConfigTool,
* Using the Maven filter "feature" to add Maven properties to code in files stored in the resources-filtered directory...specifically, updating the api.base and log4j2 configurations with the project name.

## Purpose

This project is an example that can be deployed and will run when properly configured. However, the main usage for this is a starting point for building new APIs.

Developers of an API can copy this project to get all the "boilerplate" elements for an API project. Then begin adding and modifying these elements to create the new API. 

## Configuration Properties

The configuration properties are primarily stored in the configuration property service (CPS) and are accessed by this API using the CPS connector. The configuration for the CPS connector is in the config.xml file. CPS property values are deployment environment sensitive and can also be encrypted. A sample CPS config file is provided as example-config.json. This file can be POSTed to the configuration property service to get a basic config installed. Note that the CPS connector requires the config to be found in the CPS service...otherwise the API will fail deployment.

Properties that do not change for any of the deployment environments can be stored in the src/main/resources-filtered/common.properties file. This file will be filtered by Maven which means properties can take on values from the Maven build process. Example of what is the common.property file is:

* api.base is the base uri for the API, the default is the project name taken from the pom file. This is used to form the API's base uri.
* api.semantic.version is the API syntax version, used to form the API's base uri.

The deployment environment sensitive property files are also available to store properties that are not subject to encryption and are static enough to not warrant storing in the CPS property service. These files are named {env}-config.properties (for instance DEV-config.properties). The primary usage of these files is to store the API Manager autodiscovery attributes. The ApiConfigTool will interact with the API Manager to generate and store these values in the appropriate environment property file.


## Runtime properties

There are many properties associated with the API as part of its Runtime configuration. These are set in deployment properties for CloudHub, the wrapper.conf file for standalone Mule Runtimes and in the Studio Run configuration arguments.

An example of the runtime properties needed for Studio are in the file "example-studio-run-arguments.txt".

The pom.xml file contains the Runtime properties for CloudHub deployment.

## Maven Settings

The Mule deployment assumes that certain deployment properties will come from profiles specified when the mvn command is executed. An example-settings.xml is provided as a reference for creating your own settings.xml file. The settings.xml file is a standard part of Maven and is described in its online documentation. 

Once the settings.xml file has been created, export a u and a p shell environment variables containing your Anypoint user name and password. 

## Specifying User Credentials on Command Line

Most of the following commands require your Anypoint user name and password. If you have set up the environment variables as described above, then you can specify the credentials as described below. Note that deployment commands will require Anypoint native users, single signon users will not be able to deploy to Anypoint Runtime environments.

For Windows systems, use this format for specifying your user credentials:

```
-Du=%u% -Dp=%p%
```

For Mac/OSX systems, use this format for specifying your user credentials:

```
-Du=${u} -Dp=${p}
```

### Build on local workstation

```
mvn clean install -Du= -Dp=
```

### Build and Deploy to Cloudhub Runtime:

```
mvn clean install deploy -DmuleDeploy -Denv=xxx -Pcloudhub -Du= -Dp=
```

Replacing the **xxx** with the appropriate lowercase environment name (for instance dev, test, or prd)

### Build and Deploy to Runtime Fabric (RTF):

This takes two commands,
  1) Build and publish the deployable to Exchange:
  
```
mvn clean install deploy -Pexchange -Du= -Dp=
```

  2) Deploy from Exchange to RTF:

```
mvn mule:deploy -Dmule.artifact=dummy -Denv=xxx -Prtf -Du= -Dp= 
```

### Build and Deploy to Hybrid Mule Runtime:

```
mvn clean install deploy -DmuleDeploy -Denv=xxx -Phybrid -Du= -Dp=
```

Replacing the xxx's with the appropriate environment name.

## Configuring API Manager
This template also provides a command for automatically configuring the API Manager (and Exchange if needed). All four deployment environments will include an API instance registered with client_id enforcment set as the API policy. This configuration can be modified to include different policies, but this template only implements the default policy.

To configure the api in API Manager, use one of the following Maven commands:

```
mvn clean install -Pconfig-api -Du= -Dp=
```
