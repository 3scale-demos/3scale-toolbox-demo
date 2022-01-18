# Managing APIs

## Create methods
You can create additional methods from the command line as well.

`3scale method apply [opts] <remote> <service> <method>`
~~~
$ toolbox 3scale method apply --description="New Method created by CLI" -n StateAlerts $DEST weather-alerts statealerts
Applied method id: 26

$ toolbox 3scale method list $DEST weather-alerts
ID	FRIENDLY_NAME	SYSTEM_NAME	DESCRIPTION
24	ActiveAlertsByArugment	activealertsbyarugment	Returns a list of alerts based on state as an argument.
25	ActiveAlertsByURL	activealertsbyurl	Returns a list of alerts based on state as a url.
26	StateAlerts	statealerts	New Method created by CLI
~~~

The same command will modify existing methods specified by the method to apply changes to.

~~~
$ toolbox 3scale method apply --description="Modified by CLI" -n StateAlerts $DEST weather-alerts statealerts
Applied method id: 26

$ toolbox 3scale method list $DEST weather-alerts
ID	FRIENDLY_NAME	SYSTEM_NAME	DESCRIPTION
24	ActiveAlertsByArugment	activealertsbyarugment	Returns a list of alerts based on state as an argument.
25	ActiveAlertsByURL	activealertsbyurl	Returns a list of alerts based on state as a url.
26	StateAlerts	statealerts	Modified by CLI
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

~~~
$ toolbox 3scale product import -f weather-git.yaml $DEST
~~~

This will create the application plan, but it will not create the developer's application, so that step will still need to be done manually by a developer, or it can also be done from the command line as follows:

`3scale application-plan apply [opts] <remote> <service> <plan>`
~~~
$ toolbox 3scale application apply --account=john --name="Git Weather Alerts Application" --description="Created from the CLI" --plan=git-weather-plan --service=git-weather-alerts $DEST 1234567890
~~~