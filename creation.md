# API Creation

## 1. Import complete Product

The import command will create a new complete service based on the OpenAPI Spec json file that is stored under version control: [api/alerts-spec.json](api/alerts-spec.json). Documentation for constructing a service spec file can be found in the [OpenAPI-Specification/3.0.2](https://github.com/OAI/OpenAPI-Specification/blob/main/versions/3.0.2.md) GitHub.

`3scale import openapi [opts] -d <destination> <spec>`
~~~
$ toolbox 3scale import openapi -t weather-alerts --default-credentials-userkey=dummykey -d $DEST api/alerts-spec.json
~~~

the `-t` flag specifies the internal system name, so the title field can be used as the 'friendly name' of the service. The output provides the service id and echos the service system name which will be needed to manipulate the service later. Each path will have a cooresponding method created and the name can be specified by `operationId` in the 
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
`3scale application-plan apply [opts] <remote> <service> <plan>`
~~~
$ toolbox 3scale application-plan apply -n "Weather Alerts Plan" --default $DEST weather-alerts weather-plan
~~~	

## 3. Create the application
~~~
$ toolbox 3scale application apply --account=john --name="Weather Alerts Application" --description="Created from the CLI" --plan=weather-plan --service=weather-alerts $DEST 1234567890
~~~

The user `john` is the userid of the default user created in a fresh install. The user key to access the API is `1234567890`.

## 4. Add/Adjust Policies

From the toolbox there is not yet a complete facility to append or modify policies from the command line. So most modifications need to be made within the admin console.

1. Remove the Anonymous Access policy.
1. Add a URL Rewrite policy to delete the user_key argument before passing the request to the backend.
1. Save the new policy chain.

From this step you can either deploy the changes in the admin console or use the toolbox to do so.
~~~
$ toolbox 3scale proxy-config deploy $DEST weather-alerts
~~~

## 5. Promote to production
You can either promote the product configuration from the admin console or from the command line.
~~~
$ toolbox 3scale proxy-config promote $DEST weather-alerts
~~~

## 6. Test
You can now test the paths of the new Product that has been created.

- ~~~
  $ curl 'https://weather-alerts-3scale-apicast-production.apps-crc.testing/alerts/active?area=KS&user_key=1234567890'
  ~~~
- ~~~
  $ curl 'https://weather-alerts-3scale-apicast-production.apps-crc.testing/alerts/active/area/KS?user_key=1234567890'
  ~~~

## Next Steps

1. [Inspection of APIs](inspection.md)
