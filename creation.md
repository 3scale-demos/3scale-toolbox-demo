# API Creation Demonstration

## 1. Import complete service

The import command will create a new complete service based on the OpenAPI Spec json file that is stored under version control: [alerts-spec.json](alerts-spec.json). Documentation for constructing a service spec file can be found in the [OpenAPI-Specification/3.0.2](https://github.com/OAI/OpenAPI-Specification/blob/main/versions/3.0.2.md) GitHub.

~~~
$ toolbox 3scale import openapi -d $DEST alerts-spec.json -t weather-alerts --default-credentials-userkey=dummykey -k
~~~
the `-t` flag specifies the internal system name, so the title field can be used as the 'friendly name' of the service. The output provides the service id and echos the service system name which will be needed to manipulate the service later.
~~~
Created service id: 3, name: Weather Alerts API
Service proxy updated
destroying all mapping rules
Created GET /alerts/active$ endpoint
Created GET /alerts/active/area/{state}$ endpoint
Service policies updated
~~~

If the API (under version control) is ever updated, this same command will recreate the service with the new definitions provided the name of the API in the spec file has not changed.

## 2. Create application-plan for service
~~~
$ toolbox 3scale application-plan apply $DEST weather-alerts weather-plan -n "Weather Alerts Plan" --default -k
~~~	

## 3. Create the application
~~~
$ toolbox 3scale application apply $DEST 1234567890 --account=john --name="Weather Alerts Application" --description="Created from the CLI" --plan=weather-plan --service=weather-alerts -k
~~~

The user `john` is the userid of the default user created in a fresh install. The user key to access the API is `1234567890`.

## 4. Add/Adjust Policies

From the toolbox there is not yet a facility to append or modify policies from the command line. So modifications need to be made within the admin console.

## 5. Promote to production

~~~
$ toolbox 3scale proxy-config promote $DEST weather-alerts -k
~~~

Policies will be listed when viewing the proxy configurations using the `3scale proxy-config` command, which supports `list`, `show`, `export`, `deploy`, and `promote`. However, `deploy` does not deploy from a file, it deploys the current configuration into the sandbox if there are changes pending.

# Other operations

## list methods
Importing from an OAS file will automatically create the defined methods and mapping rules. It is possible to view and manipulate them.
~~~
$ toolbox 3scale method list $DEST weather-alerts -k
ID	FRIENDLY_NAME	SYSTEM_NAME	DESCRIPTION
18	AlertsByState	alertsbystate	Returns a list of alerts based on state.
~~~

## create metrics

~~~
$ toolbox 3scale metric create -t state-alerts --description="Number of alerts" $DEST weather-alerts "State Alerts" -k
~~~
