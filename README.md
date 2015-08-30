POSIX service manager for non-root users. :angel:

This gives you a way to launch, shut down, and monitor long-running services and servers as a non-root user without having to modify any system level files outside of your home directory.

## Install ##

	$ curl https://raw.githubusercontent.com/chr15m/angel/master/angel > ~/bin/angel
	$ chmod 700 ~/bin/angel

These above commands assume you have a `~/bin` directory in your path. On most modern POSIX systems if you create that directory it will be added to your path automatically after you log in again.

## Commands ##

The following commands are idempotent.

	$ angel install 15

Install the `angel` service manager so that it runs as a daemon automatically.

This creates new lines or modifies the existing lines in your user crontab to run a check every X minutes (default 15) to make sure angel is running and launch it if not. Also adds an `@reboot` entry if your cron supports it so that angel will launch at startup.

	$ angel service servicename '/path/to/my/service &'

Sets up a new service, or modifies an existing service called `servicename` which is launched with the `/path/to/my/service &` command supplied. You need the ampersand if the service does not automatically daemonize.

This creates a file `~/.angel-servicename.cfg` with details about the service to be run and the current desired state of the service. For each service you set up there will be a different config file. The default status of the process is `stopped`.

	$ angel start servicename

Starts the service defined by `servicename` as above. This modifies the file `~/.angel-servicename.cfg` to change the `status` to `running`.

The angel service manager that is running in the background (installed in cron) will read all angel config files and start any service that is not running but has a status of `running`. It will also kill any service that is running but has a status of `stopped`.

	$ angel stop servicename

Stops the service defined by `servicename`. Changes the `status` line to `stopped`.

	$ angel status servicename

Returns `running` or `stopped` or `not-found` depending on the actual status of the service (not the config file status). If there is an `*` after the word it means that the program is not in the state requested. Check the log file for errors starting or killing your service in this case.

	$ angel status

Returns `running` or `stopped` depending on the status of the angel monitoring process.

## Log ##

The output of the `angel` process is written to `~/.angel.log`. When your crontab is changed the lines removed will be logged here in case you need them.
