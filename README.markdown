== Ruby on Rails on WebLogic 11gR2

This sample app demonstrates how to configure and deploy a Ruby on Rails 
app on Oracle WebLogic.

Tested with software versions

* development machine
jruby 1.7.4 (1.9.3p392) 2013-05-16 2390d3b on Java HotSpot(TM) 64-Bit Server VM 1.6.0_45-b06-451-11M4406 [darwin-x86_64]
Oracle Database Instant Client 11.2

* server
WebLogic 10.3.6 (aka 11gR2) on RHEL 6.4 64bit
Oracle Database 11.2.0.3

I use rbenv (http://rbenv.org) for my ruby environments.  Without rbenv 
you may need to use jruby -S <command> (e.g. jruby -S rails... instead of 
rails...).



## Tasks and configurations


### Create application
rails _3.2.13_ new myapp --database=jdbc


### Use Oracle JDBC driver

On you development machine you need an Oracle database client - the 
instant client is definitelly sufficient.
The JDBC driver should be kept outside your project, because there 
is already one on the server and because of Oracle licensing issues.
Add this environment variables:

##  Oracle database
export SQLPATH="/usr/local/oracle/current"
export ORACLE_JDBC_JAR=${SQLPATH}/ojdbc6.jar
##  needed for rake and oracle jdbc driver
export SKIP_AR_JDBC_RAKE_REDEFINES=1

In the environment initializer (config/environments/*.rb) you now 
need to add

  require 'java'
  $CLASSPATH << ENV['ORACLE_JDBC_JAR']


### Create a datasource in Weblogic

In case you will run on a RAC (Cluster) use a GridLink datasource config
The datasource will be fetched by JNDI.
Check in 


### Configure your application database connections

Development and test are configured to use your local database connection, 
production for the Weblogic setup.
Have a look in config/database.yml 


### Asset precompilation

You need to deploy precompiled assets.  To avoid the precompile process 
initialzing your app in production mode on the deevelopment machine set  
  config.assets.initialize_on_precompile = false
in config/application.rb


### Resolving class version conflicts

Weblogic uses an older version of an included lib (don't remember which one 
any more).  To enable your app loading your libs version you need to add the 
included weblogic.xml part.


rails new my_app --database=jdbc
cd my_app
rails generate scaffold Post name:string title:string content:text
rake db:migrate SKIP_AR_JDBC_RAKE_REDEFINES=1
warble config
rake assets:precompile
warble war



