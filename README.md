Jsign - Java implementation of Microsoft Authenticode for Unbound EKM
=====================================================================

Jsign is a Java implementation of Microsoft Authenticode that lets you sign
and timestamp executable files for Windows. Jsign is platform independent and
provides an alternative to native tools like signcode/signtool on Windows
or the Mono development tools on Unix systems.

Added support for Unbound-tech security provider.

See https://ebourg.github.com/jsign for more information.

## Prerequisites
* EKM client is installed
* Ant (Version >= 1.93) is installed. Refer to https://ant.apache.org/bindownload.cgi for further details
* The `lib:org.apache.ivy.ant` task is installed. Refer to http://ant.apache.org/ivy/history/2.2.0/ant.html for further details

## Build
Add the Unbound Java Security Provider Jar (ekm-java-provider-2.0) to root directory of the project and run `ant`.

## Usage

### Using CLI 

To sign a `<file-name>` using key named `<key-alias>`, use the following syntax:

```
java -jar .\ub-jsgin-cli.jar 
[--partition <EKM Partition Name>]     // if not specified - uses the first found partition
--storetype dyadic                     // mandatory
--alias <key-alias>                    // alias of key name used for signing 
<file-name>                            // file name                     
```

Example:

In the install directory, run

```
java -jar .\ub-jsgin-cli.jar --partition part1 --storetype dyadic --alias key-alias file-name
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
      <taskdef name="UbSignTask" classname="net.jsign.PESignerTask" classpath="ub-jsgin-ant.jar" />
      <UbSignTask file="file-name" partition="part1" alias="key-alias" storetype="dyadic" />
   </target>
</project>
```
