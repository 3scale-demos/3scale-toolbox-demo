# Inspecting APIs

## List methods

Importing from an OAS file will automatically create the defined methods and mapping rules as defined inside the spec. It is possible to view and manipulate them.

~~~
$ toolbox 3scale method list $DEST weather-alerts
ID	FRIENDLY_NAME	SYSTEM_NAME	DESCRIPTION
24	ActiveAlertsByArugment	activealertsbyarugment	Returns a list of alerts based on state as an argument.
25	ActiveAlertsByURL	activealertsbyurl	Returns a list of alerts based on state as a url.
~~~

## List metrics

By default only the `Hits` metric will be created with a new Product.

~~~
$ toolbox 3scale metric list $DEST weather-alerts -k
ID	FRIENDLY_NAME	SYSTEM_NAME	UNIT	DESCRIPTION
22	Hits	hits	hit	Number of API hits
~~~

## Export a Product
When exporting a product the resulting file is inside the container, which is one reason why we set up the mounted volume in our [alias](README.md#environment-setup) for the toolbox. This allows us to get the output file from the export inside our current working directory.

~~~
$ toolbox 3scale product export -f weather.yaml $DEST weather-alerts -k
$ ls weather.yaml 
weather.yaml
~~~

This file can be placed in git or some other version control system, or it can be used to preserve an 'air-gap' between a development and production system.


# Inspecting the system

## Services
You can also use the toolbox to do inspection of higher order objects as well.

~~~
$ toolbox 3scale service list $DEST
ID	NAME	SYSTEM_NAME
2	API	api
6	Weather Alerts API	weather-alerts
~~~

~~~
$ toolbox 3scale service show $DEST weather-alerts
ID	NAME	STATE	SYSTEM_NAME	END_USER_REGISTRATION_REQUIRED	BACKEND_VERSION	DEPLOYMENT_OPTION	SUPPORT_EMAIL	DESCRIPTION	CREATED_AT	UPDATED_AT
6	Weather Alerts API	incomplete	weather-alerts	(empty)	1	hosted	admin@3scale.apps-crc.testing	Get state-level weather alerts	2022-01-17T17:37:10Z	2022-01-17T21:43:46Z
~~~