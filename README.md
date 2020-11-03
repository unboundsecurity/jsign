Jsign - Java implementation of Microsoft Authenticode for Unbound Key Control
=============================================================================

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

    ```
    --storepass '{"username":"Signer1", "password":"Signer1!"}'
    --storepass '{"username":"USER",    "password":"User100!"}'
    ```
    
**Note:** The JSON string must be delimited by single quotes or any other method as applicable in your shell.

## Import a Code Signing Certificate
Refer to the corresponding topic in [Import a Certificate from a File using UCL](https://www.unboundtech.com/docs/UKC/UKC_Code_Signing_IG/HTML/Content/Products/UKC-EKM/UKC_Code_Signing_IG/SigningCertificates.htm#h3_3).


## Build
Add the Unbound Java Security Provider (`ekm-java-provider-2.0.jar`) file to root directory of the project and run `ant`.

## Usage

### Using CLI 

To sign a `<file-name>` using key named `<key-alias>`, use the following syntax:

```
java -jar .\ub-jsign-cli.jar 
[--partition <UKC Partition Name>]     // if not specified - uses the first found partition
--storetype dyadic                     // mandatory
--alias <key-alias>                    // alias of key name used for signing 
<file-name>                            // file name                     
```

Example:

In the install directory, run

```
java -jar .\ub-jsign-cli.jar --partition part1 --storetype dyadic --alias key-alias file-name
```

Use the following syntax to sign Windows PE files, such as *.exe* and *.dll* files:

```
java -jar <WORKING DIRECTORY>/usr/share/jsign/ub-jsign-cli.jar \
 --keystore <PKCS#11 CONFIG FILE that specifies a particular UKC partition> \
 --storepass <see "User Credentials" above> \
 --storetype PKCS11 \
 --alias <KEYNAME> \
 -t http://timestamp.digicert.com  \
 <EXE or DLL to be signed>
```

In the following example, the client has certificates for two partitions:
```
ucl partition list
 Partition 0: Encrypt1
 Partition 1: CodeSign1
```

The *CodeSign1* partition has:

* A certificate and its matching private key, both named *key-pfx*: 
    ```
    ucl list -p CodeSign1
     Partition 1 CodeSign1: 2 objects found
     Private RSA key: UID=c5b67c919d628027 Name="key-pfx"
     Certificate: UID=3a49836e629d7fd8 Name="key-pfx"
    ```
* The following users: 
    ```
    ucl user list -p CodeSign1
         Signer100
         so
         user
    ```

To sign a file on behalf of user *Signer100* using the private key associated with the *key-pfx* certificate:

1. Make sure that the PKCS #11 configuration file refers to `slot=1` according to the listed partition number (`Partition 1: CodeSign1`). For example: 
    ```
    cat ~/CodeSign1Pkcs11.cfg
    name = DyadicPKCS
    library = <path to libekmpkcs11.so>/libekmpkcs11.so
    slotListIndex = 1
    ```
    Note: For the platform-dependent location of the libekmpkcs11.so, refer to [Path to PKCS#11 Library](https://www.unboundtech.com/docs/UKC/UKC_Integration_Guide/HTML/Content/Products/UKC-EKM/UKC_Integration_Guide/FrontMatter/Integration_with_UKC.htm#PKCS).
2. Prepare the following string with Signer100 credentials: 
    `'{"username":"Signer100", "password":"Signer100!"}'`
3. Sign the file: 
    ```
    java -jar ~/usr/share/jsign/ub-jsign-cli.jar \
    --keystore ~/CodeSign1Pkcs11.cfg \
    --storepass '{"username":"Signer100", "password":"Signer100!"}' \
    --storetype PKCS11 \
    --alias key-pfx \
    -t http://timestamp.digicert.com  \
    ~/setup1.exe
    ```
   Response:
   
   `Adding Authenticode signature to setup1.exe`



### Using Ant

Add the Unbound Java Security Provider (*ekm-java-provider-2.0.jar*) file found in the UKC Client distribution to the root directory of the project and run `ant`.

In the install directory, run:

```
 ant -buildfile .\jsign.xml
```
Where the `jsign.xml` contains the following:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="UbSign" basedir="." default="ubsign">
   <target name="ubsign">
      <taskdef name="UbSignTask" classname="net.jsign.JsignTask" classpath="ub-jsign-ant.jar" />
      <UbSignTask file="file-name" partition="part1" alias="key-alias" storetype="dyadic" />
   </target>
</project>
```
