# Tietoevry Data Catalog - Scanner Setup Quick Guide

## Documentation
https://zeenea-platform-docs.netlify.app/docs/zeenea-scanner-setup

## Software Dependencies

The current Scanner version requires **Java 11**. OpenJDK or Oracle JDK can be installed indifferently. Check current version with 
```bash
java --version
```

## Download Latest Scanner Version
```bash
curl -L -H "X-API-SECRET: $APISECRET" "https://tietoevry.zeenea.app/studio/api-v1/agent/get-agent" -o scanner.tar.gz
```
Note: `$APISECRET` will be sent separately.

## Naming Your Scanner

1. Create a text file named `agent-identifier` in the main scanner directory
2. Enter only the desired scanner name in this file (on a single line with no other text)
3. If upgrading from a previous scanner version, you can simply copy the existing `agent-identifier` file to maintain the same scanner name


## Configuration 

### Initial setup

1. Locate the template file at `/conf/application.conf.template`
2. Copy this file and rename it to `/conf/application.conf` 
3. Edit the file to update these values:
   - Set `zeenea-url` to `"https://tietoevry.zeenea.app"`
   - Replace `$APISECRET` with the actual API secret value (which will be provided to you separately)

Your completed configuration should look like this:

```
################### ZEENEA CONNECTION ###################
zeenea-url = "https://tietoevry.zeenea.app"
authentication {
    api-key {
        secret = "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJ6ZWVuZWEiLCJhdWQiOiJ6..."
    }
}
```

### Change frequency of execution (optional)

https://zeenea-platform-docs.netlify.app/docs/zeenea-scanner-setup#process-configuration-optional


### Create a docker image for your scanner (optional)

https://zeenea-platform-docs.netlify.app/docs/zeenea-scanner-docker


## Connectors

### Installing a Connector
1. Download the connector plugin (zip file) from the connector [download links](https://zeenea-platform-docs.netlify.app/docs/zeenea-connectors-list)
2. Place the zip file in the `/plugin` directory (no need to extract)
3. Download the [connector configuration](https://github.com/zeenea/connector-conf-templates/tree/main/templates) file and place it in the `/connections` directory
4. Configure the connector according to its documentation. Each connector has its own [documentation](https://zeenea-platform-docs.netlify.app/docs/zeenea-connectors-list)


⚠️  Remember to set  `catalog_code`. Your specific `catalog_code` will be provided to you. 

### Example connection-configuration for SQL Server ### 
```
name = "SQL Server Demo"
code = "sql_server_demo"
connector_id = "SqlServer"

# Define the catalog code associated with the connection ("default" when empty).
catalog_code = "unbolt"

connection {
    # JDBC URL
    url = "jdbc:sqlserver://tietoevry-test.database.windows.net:1433;database=tietoevry-example-db;user=te-uat-sql-server-admin@tietoevry-test;password=***;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
    username = "te-uat-sql-server-admin"
    password = "***"
}
```

For complete list of configuration options for this example, see [SQL Server Connector documentation page](https://zeenea-platform-docs.netlify.app/docs/zeenea-connector-sqlserver).


## Manually Run inventory
By default, the scanner runs every 24 hours. 
When installing and configuring new connectors, it can be useful to trigger the inventory manually, to verify that the connector has been installed and configured correctly.
To do this, please run:

```bash
./bin/zeenea-scanner inventory -c "$connection_code"
```
where `$connection_code` is the `code` value from the connection configuration. 

_Example: `./bin/zeenea-scanner inventory -c "sql_server_demo"`_

After a succesfull inventory, there should be items available from the connection for import in Zeenea Studio. 


## Starting the Scanner

### Simple start (test only) ###

The `./bin/zeenea-scanner` script is used to start the Scanner.



### Run as a background service ###
It is important to allocate enough memory to the JVM. 8 GB are recommended.


```bash
nohup ./bin/zeenea-scanner -J-Xmx8g &
```
**⚠️ Important**: Restart the scanner after any configuration changes for them to take effect.

## Logs
Scanner logs are available at: `/logs/scanner.log`



## Setup Checklist

- [ ] Java 11 installed
- [ ] Scanner downloaded and extracted
- [ ] `agent-identifier` file created with scanner name
- [ ] `application.conf` file created from template with correct API secret
- [ ] Connector plugin(s) downloaded and placed in `/plugin` directory
- [ ] Connection configuration file(s) created in `/connections` directory
- [ ] Connection configured according to documentation. Remember to set correct `catalog_code`
- [ ] Scanner started with sufficient memory allocation
- [ ] Manual inventory run tested
- [ ] Log file checked for any errors
