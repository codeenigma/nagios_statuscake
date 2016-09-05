# nagios_statuscake
Simple Nagios plugin for StatusCake uptime checks in Nagios.

Based on the Gist posted here by David R. Albrecht (eldavido) for Pingdom checks:
https://gist.github.com/eldavido/5374007

This simple Ruby script allows Nagios to interrogate StatusCake for uptime status of websites by test ID. This is handy if you want to have consolidated alerting via Nagios but also want to make use of StatusCake for uptime monitoring.

# Installation
Copy the Ruby script, `check_statuscake`, to your Nagios plugins directory and make sure it is executable by the Nagios user.
