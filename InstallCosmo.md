Cosmo
===============

# Install #

## Download cosmo ##

    # git clone https://github.com/openmash/cosmo
    # cd cosmo
    # git checkout relocations
    
## Install cosmo ##

    # mvn clean install -Dmaven.test.skip=true
    # cd cosmo-webapp
    # mvn jetty:run-war -Djetty.port=9090 -Pdevelopment
    
## Run cosmo ##

Since maven will be shut down when ssh session is killed the best will be to run it with nohup

    # nohup mvn jetty:run-war -Pdevelopment &

Go to `http://<your-domain>:9090/admin`.

# Known issues #

* Logs are printed to console, so nohup.out / nohup.log will contain recent entries
* user `root` has some problems with creating calendars and events. We strongly encourage to create users. Those users are able to create calendars and events.



