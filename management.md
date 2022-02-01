# Managing APIs

## Create methods
You can create additional methods from the command line as well.

`3scale method apply [opts] <remote> <service> <method>`
~~~
$ toolbox 3scale method apply --description="New Method created by CLI" -n StateAlerts $DEST weather-alerts statealerts
Applied method id: 26
~~~

The same command will modify existing methods specified by the method to apply changes to.

~~~
$ toolbox 3scale method apply --description="Modified by CLI" -n StateAlerts $DEST weather-alerts statealerts
Applied method id: 26
~~~

The toolbox can also delete methods by system-name as well.

`3scale method delete [opts] <remote> <service> <method>`
~~~
$ toolbox 3scale method delete $DEST weather-alerts statealerts
Method id: 26 deleted
~~~

## Create metrics
If necessary, additional metrics can be created beyond the default `Hits` metric.

`3scale metric apply [opts] <remote> <service> <metric>`
~~~
$ toolbox 3scale metric apply --name="State Alerts" --description="Number of alerts" $DEST weather-alerts state-alerts
~~~

## Import complete products
After exporting a product and storing the yaml in version control or transporting it to the target server by other means, the next step is importing the product onto the target system.

When importing a product the needed file is in your local directory and needs to be mounted inside the container. Using the `-v` argument with the `podmand run` allows us to do that. It is part of the [alias](README.md#environment-setup) set up for the toolbox. This allows us to get the input file from our current working directory inside the working directory of the container.

### 1. Import from the yaml file
~~~
$ toolbox 3scale product import -f git-weather.yaml $DEST
~~~

### 2. Create ActiveDocs
Importing the product will not create the ActiveDocs for the product. However, the same process can be used to create the ActiveDocs as the raw creation of a product using the `import openapi` function with the same system-name.

~~~
$ toolbox 3scale import openapi -t git-weather-alerts --default-credentials-userkey=dummykey -d $DEST git-alerts-spec.json
Updated service id: 4, name: Git-controlled Weather Alerts API
Service proxy updated
destroying all mapping rules
Created GET /alerts/active$ endpoint
Created GET /alerts/active/area/{state}$ endpoint
Service policies updated
~~~

### 2. Create developer application
The application plan gets created with the product import, but it will not create any developer's applications. That step will still need to be done manually by a developer, or it can also be done from the command line as follows:

~~~
$ toolbox 3scale application apply --account=john --name="Git Weather Alerts Application" --description="Created from the CLI" --plan=git-weather-plan --service=git-weather-alerts $DEST 1234567890
~~~

### 3. Promote to production
You can either promote the product configuration from the admin console or from the command line.
~~~
$ toolbox 3scale proxy-config promote $DEST git-weather-alerts
~~~

### 4. Test
You can now test the paths of the new Product that has been created.

- ~~~
  $ curl 'https://git-weather-alerts-3scale-apicast-production.apps-crc.testing/alerts/active?area=KS&user_key=1234567890'
  ~~~
- ~~~
  $ curl 'https://git-weather-alerts-3scale-apicast-production.apps-crc.testing/alerts/active/area/KS?user_key=1234567890'
  ~~~