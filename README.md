# Cassandra Java Driver Kerberos Authenticator

A GSSAPI authentication provider for the [Cassandra Java driver](https://github.com/datastax/java-driver).
This driver plugin is intended to work with the 
[Cassandra kerberos authenticator](https://github.com/instaclustr/cassandra-kerberos) plugin for 
[Apache Cassandra](https://cassandra.apache.org/).

## Build

Run `mvn clean package`

## Use

**Note:** *This authenticator plugin is not yet hosted in Maven Central, so you will need to manually build & 
include it in your application's classpath.*

### Pre-requisite setup steps

- A Kerberos KDC server is required
- Follow the instructions [here](https://github.com/instaclustr/cassandra-kerberos) to configure a Cassandra cluster for Kerberos authentication.
- Configure the application server's Kerberos configuration file:

    An example `/etc/krb5.conf` for the `EXAMPLE.COM` realm:
    
    ```$ini
    [logging]
    default = FILE:/var/log/krb5libs.log
    
    [libdefaults]
     default_realm = EXAMPLE.COM
     dns_lookup_realm = false
     dns_lookup_kdc = false
    
    [realms]
     EXAMPLE.COM = {
      kdc = kdc.example.com
      admin_server = kdc.example.com
    }
    
    [domain_realm]
     .example.com = EXAMPLE.COM
     example.com = EXAMPLE.COM
    ```
    
    See [here](http://web.mit.edu/kerberos/www/krb5-latest/doc/admin/conf_files/krb5_conf.html) for further details.
    
    
### How to use the authenticator plugin

**Note:** *Please read the javadoc for full details on how to configure & use the plugin.*

The plugin works with the [Cassandra Java driver](https://github.com/datastax/java-driver):

```$java
Cluster cluster = Cluster.builder()
                      .addContactPoint(hostname)
                      .withAuthProvider(KerberosAuthProvider.builder().build()
                      .build();
```

A JAAS config file is also required. The following example retrieves a TGT from the local Kerberos ticket cache:

```
CassandraJavaClient {
   com.sun.security.auth.module.Krb5LoginModule required useTicketCache=true;
};
```

The location of the JAAS config file can be provided via the `java.security.auth.login.config` system property
(e.g. `java -Djava.security.auth.login.config=/path/to/jaas.conf -jar MyApplication.jar`).


