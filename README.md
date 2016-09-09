# nagios_statuscake
Simple Nagios plugin for StatusCake uptime checks in Nagios.

Based on the Gist posted here by David R. Albrecht (eldavido) for Pingdom checks:
https://gist.github.com/eldavido/5374007

This simple Ruby script allows Nagios to interrogate StatusCake for uptime status of websites by test ID. This is handy if you want to have consolidated alerting via Nagios but also want to make use of StatusCake for uptime monitoring.

# Installation
Copy the Ruby script, `check_statuscake`, to your Nagios plugins directory (in our case /usr/lib/nagios/plugins) and make sure it is executable by the Nagios user.

Edit your Nagios `commands.cfg` file in the Nagios installation directory (usually something like /etc/nagios) and add the command to execute the script:

```
# StatusCake checks
define command{
        command_name    check_statuscake
        command_line    /usr/lib/nagios/plugins/check_statuscake -u USERNAME -k 'APIKEY' -i $ARG1$
}
```

Your username is on this settings page: https://www.statuscake.com/App/User.php

And your API key can be found on this page: https://www.statuscake.com/App/APIKey.php

You now have the necessary script in place and Nagios is aware of the command and knows how to execute it. We have also provided a sample service definition, which needs copying to the designated config directory for Nagios. If you do not know where the config directory is, find the file `nagios.cfg` and inspect the line `cfg_dir=` - it is usually something like `/etc/nagios/conf.d`.

**Important** - this file is a sample only, your check_period, contact_groups, etc. may be different.

Optionally, you may also download and use the the `check_statuscake_all_checks` script. This is a simple Bash script that calls `check_statuscake` for each test returned by the StatusCake API for a given account username and API key. Installation is straightforward, just copy `check_statuscake_all_checks` to the Nagios plugins directory and make sure it is executable by the Nagios user. Usage is simply:

```
/usr/lib/nagios/plugins/check_statuscake_all_checks USERNAME APIKEY
```

As long as `check_statuscake` is there in the same directory and executable, it will just work. You could of couse make a similar service definition and command for this check, if you wanted to have Nagios use it, but it's probably more useful as a standalone script for a "quick check" of your StatusCake tests.

Now to set up some actual checks in Nagios...

# Checks
To create checks create a new `statuscake_checks.cfg` file in the designated config directory, as described above. You can add checks to that file. Here is an example of a StatusCake check in the Nagios config:

```
define service {
        host_name                       monitor1.codeenigma.net
        service_description             Code Enigma StatusCake
        check_command                   check_statuscake!1335305
        use                             statuscake-service
}
```

Note the `check_statuscake!1335305` line - this tells Nagios to use the `check_statuscake` command we defined above and pass it the argument `1335305`, which is the ID of this particular test. You can get your test IDs either from the edit URL of the test in the StatusCake web GUI (e.g. https://www.statuscake.com/App/AllStatus.php?tid=1335305) or you can retrieve a list of all your tests via the API using the Get All Tests method: https://www.statuscake.com/api/Tests/Get%20All%20Tests.md

The curl command for Get All Tests looks like this:

`curl -H "API: YOURAPIKEY" -H "Username: YOURUSERNAME" -X GET https://www.statuscake.com/API/Tests/`
