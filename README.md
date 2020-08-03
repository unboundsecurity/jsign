Jsign - Java implementation of Microsoft Authenticode for Unbound EKM
=====================================================================

Jsign is a Java implementation of Microsoft Authenticode that lets you sign
and timestamp executable files for Windows. Jsign is platform independent and
provides an alternative to native tools like signcode/signtool on Windows
or the Mono development tools on Unix systems.

Added support for Unbound-tech security provider.

See https://ebourg.github.com/jsign for more information.

## Prerequisites
* The UKC client must be installed. See [here](https://www.unboundtech.com/docs/UKC/UKC_User_Guide/HTML/Content/Products/UKC-EKM/UKC_User_Guide/Installation/ClientInstallation.html#h2_1) for more information.

* Ant (Version >= 1.93) is installed. 
To install on RH/Centos, use `yum install ant`.
Refer to https://ant.apache.org/bindownload.cgi for further details.

* The `lib:org.apache.ivy.ant` task is installed. 
To install on RH/Centos, use `yum install ivy`.
Refer to http://ant.apache.org/ivy/history/2.2.0/ant.html for further details.

## Prepare Jsign User Credentials
Use the `--storepass` parameter to provide credentials for the user performing jsign as follows:
* `--storepass null` – indicates the default USER with the default (empty) password.
* `--storepass <password of USER>` – indicates the default USER with a non-empty password.

    For example:

    `--storepass User100!`

* `--storepass '<JSON string>'` provides complete credentials in a JSON-formatted string:

     `{"username":"<partition user name>", "password":"<user password>"}`
 
    For example:

    `--storepass '{"username":"Signer1", "password":"Signer1!"}'`

    `--storepass '{"username":"USER",    "password":"User100!"}'`

**Note:** The JSON string must be delimited by single quotes or any other method as applicable in your shell.




## Build
Add the Unbound Java Security Provider (`ekm-java-provider-2.0.jar`) file to root directory of the project and run `ant`.

## Usage

### Using CLI 

To sign a `<file-name>` using key named `<key-alias>`, use the following syntax:

```
java -jar .\ub-jsign-cli.jar 
[--partition <EKM Partition Name>]     // if not specified - uses the first found partition
--storetype dyadic                     // mandatory
--alias <key-alias>                    // alias of key name used for signing 
<file-name>                            // file name                     
```

Example:

In the install directory, run

```
java -jar .\ub-jsign-cli.jar --partition part1 --storetype dyadic --alias key-alias file-name
```

### Using Ant

In the install directory, run

```
 ant -buildfile .\jsign.xml
```
Whereas the `jsign.xml` is

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="UbSign" basedir="." default="ubsign">
   <target name="ubsign">
      <taskdef name="UbSignTask" classname="net.jsign.JsignTask" classpath="ub-jsign-ant.jar" />
      <UbSignTask file="file-name" partition="part1" alias="key-alias" storetype="dyadic" />
   </target>
</project>
```
