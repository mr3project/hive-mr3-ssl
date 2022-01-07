hive-mr3-ssl
============

This repository contains a script `generate.sh` which generates certificates and secrets
(TrustStores and KeyStores) for running Hive on MR3 on Kubernetes in secure mode.

Requirements
============

* Java 1.8 or higher
* Hadoop binary distribution for executing `hadoop credentials`
* Java keytool
* openssl 

The environment variables `JAVA_HOME` and `HADOOP_HOME` should be set before executing the script.

Parameters
==========

The user should set the following variables in the script `generate.sh`.

* `NAMESPACE`: namespace of the Kubernetes cluster in which Hive on MR3 runs
* `HOST`: host name corresponding to the IP address associated with Service resources for HiveServer2, Ranger, Timeline server, Superset
* `VALID_DAYS`: period in which KeyStore and TrustStore files are valid
* `BEELINE_KEYSTORE_PASSWORD`: password for the Beeline KeyStore (which is distributed to end users)

The following variables are optional.

* `PASSWORD`: password for KeyStores and TrustStores.
If not set, the script uses a random string for the password.

* `METASTORE_DATABASE_PASSWORD`: password for connecting to the Metastore database, i.e., the database backing Metastore.
This password is usually set in the configuration key `javax.jdo.option.ConnectionPassword` in `hive-site.xml`.
If `METASTORE_DATABASE_PASSWORD` is set, `javax.jdo.option.ConnectionPassword` can be set to `_` in `hive-site.xml`.

* `COMMON_NAME`: host name in the Kerberos service principal for HiveServer2.
For example, if `hive/foo@BAR` is the Kerberos service principal,
`COMMON_NAME` should be set to `foo`.
This is required for using Python clients when connecting to HiveServer2.

The following variables should be set if the connection to the Metastore database is secure.

* `METASTORE_DATABASE_HOST`: host name for the Metastore database
* `METASTORE_MYSQL_CERTIFICATE`: certificate for connecting to the Metastore database

The following variables should be set if the connection to the Ranger database is secure.

* `RANGER_DATABASE_HOST`: host name for the Ranger database
* `RANGER_MYSQL_CERTIFICATE`: certificate for connecting to the Ranger database 

The following variable should be set if the connection to S3 is secure.

* `S3_CERTIFICATE`: certificate for connecting to S3

Output Files
============

The following files are used by HiveServer2, Metastore, Ranger, and Timeline server.
`hivemr3-ssl-certificate.jceks` is for the configuration key `hadoop.security.credential.provider.path` in
`core-site.xml`.
Check the usage of `hivemr3-ssl-certificate.jceks` and `hivemr3-ssl-certificate.jks`
in [Enabling SSL](https://mr3docs.datamonad.com/docs/k8s/advanced/enable-ssl/) for more details.

* `hivemr3-ssl-certificate.jceks`
* `hivemr3-ssl-certificate.jks`
* `.hivemr3-ssl-certificate.jceks.crc`

The following file is used to update the certificates for the Metastore and Ranger databases
when the connection is secure.
Check the usage of `mr3-ssl.pem`
in [Enabling SSL](https://mr3docs.datamonad.com/docs/k8s/advanced/enable-ssl/) for more details.

* `mr3-ssl.pem`

The following file is distributed to end users running Beeline.
Check the usage of `beeline-ssl.jks`
in [Enabling SSL](https://mr3docs.datamonad.com/docs/k8s/advanced/enable-ssl/) for more details.

* `beeline-ssl.jks`

The following files are used for secure shuffle between ContainerWorkers.
See [Using the MR3 Shuffle Handler](https://mr3docs.datamonad.com/docs/mr3/guide/use-shufflehandler/) for more details.

* `mr3-keystore.jks`
* `mr3-truststore.jks`

The following files are used for secure connection to Superset (using HTTPS).

* `superset.key`
* `superset.crt`

