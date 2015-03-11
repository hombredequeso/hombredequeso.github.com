---
layout: post
title: Flyway Installation on Windows
---

#{{ page.title }}#

<p class="meta">12 March 2015</p>

As the existing homebrew database migration tool started to show it age and certain limitations, our team recently went looking for a simple, existing solution to the sql database migration requirement. Directed to [Flyway](http://flywaydb.org/) based on the [Thoughtworks Tech Radar](http://www.thoughtworks.com/radar/tools) 'Adopt' recommendation, we set out to replace the existing tool.

Following outlines the steps required to get Flyway working on a windows machine, using windows authentication.

# Prerequisites
Ensure java is installed. Test in a command line:
java -version

# Download Command Line Flyway
[Download command line flyway](http://flywaydb.org/getstarted/download.html) and unzip it.

This outline is based on using: flyway-commandline-3.1.zip

# Create a Test Database
Create a test sql server database in the default sql instance on your local machine.
In this instance, the database was called: flywaytest

# Configure Flyway
Go to the file: flyway-3.1/conf/flyway.properties
And set the flyway url line like this:
    flyway.url=jdbc:jtds:sqlserver://localhost:1433/flywaytest 

Test whether flyway is correctly working. The simplest way to do this is to run flyway (in fact, the flyway.cmd script):
    > flyway info

It is highly likely this will not work, resulting in something like the following error:

    > flyway info
    Flyway (Command-line Tool) v.3.1

    ERROR: Unable to obtain Jdbc connection from DataSource (jdbc:jtds:sqlserver://localhost:1433/flywaytest ) for user
    '': I/O Error: SSO Failed: Native SSPI library not loaded. Check the java.library.path system property.

# Install ntlmauth.dll (if required)
If so, download the jtds driver distribution of the same version as the jtds driver found in flyway-3.1/drivers
(in this instance, that was: jtds-1.3.1.jar).

See [jTDS - SQL Server and Sybase JDBC driver](http://sourceforge.net/projects/jtds/files/jtds/)

In this instance, the appropriate version was [here](http://liquidtelecom.dl.sourceforge.net/project/jtds/jtds/1.3.1/jtds-1.3.1-dist.zip)

Unzip it, and find the ntlmauth.dll appropriate to the java being used on your machine. In this case, 32-bit java was used, so the dll was:
jtds-1.3.1-dist\x86\SSO\ntlmauth.dll
Place this file somewhere where it can be loaded by java, namely somewhere specified in the path environment variable.

# Watch it run
Try running flyway, and this time it should work.

    > flyway info
    Flyway (Command-line Tool) v.3.1

    Database: jdbc:jtds:sqlserver://localhost:1433/flywaytest  (Microsoft SQL Server 10.0)

    +---------+-------------+---------------------+---------+
    | Version | Description | Installed on        | State   |
    +---------+-------------+---------------------+---------+
    | No migrations found                                   |

If there are problems with the sql driver, note that there are (at least) two different sql server drivers for java. The one used here is the jtds driver.
It is different from the [Microsoft driver](https://msdn.microsoft.com/en-us/sqlserver/aa937724.aspx)
These drivers have different, but very similar, connection strings and other settings, so be sure to check carefully which driver you are reading about on stackoverflow etc.

# Use it
At this point, flyway itself is installed and working.
For details on actual usage of flyway, see the [documentation](http://flywaydb.org/documentation/commandline).

