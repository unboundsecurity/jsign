Jsign - Java implementation of Microsoft Authenticode
=====================================================

Jsign is a Java implementation of Microsoft Authenticode that lets you sign
and timestamp executable files for Windows. Jsign is platform independent and
provides an alternative to native tools like signcode/signtool on Windows
or the Mono development tools on Unix systems.

Added support for Unbound-tech security provider.

See https://ebourg.github.com/jsign for more information.


## Build
Add the Unbound Java Security Provider Jar (ekm-java-provider-2.0) to root directory of the project and run `ant`.

## Usage
### cli 

`java -jar .\ub-jsgin-cli.jar --partition part1 --storetype unbound --alias key-alias file-name`

### ant
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="UbSignExe" basedir="." default="ubsign">
   <target name="ubsign">
      <taskdef name="UbSignExe" classname="net.jsign.PESignerTask" classpath="ub-jsgin-ant.jar" />
      <UbSignExe file="wineyes.exe" partition="p1" alias="test" storetype="unbound" />
   </target>
</project>
```
