---
title: Generate a Custom Java Truststore
date: 2024-10-24 03:58:00 +0700
categories: [Java_Tips]
tags: [java, security]
---

## Generate a Custom Java Truststore

This question comes up occasionally from developers that need to connect to a
remote server that requires SSL/TLS using a self-signed certificate.

In these cases, a "root CA" may have been generated and provided to the
developer, which needs to be "trusted" for their application to connect. These
files typically end in `.crt` and contain information about the authority, as
well as a cryptographic signature that is used to verify the authenticity of
any certificate generated by that CA.

Different programming languages have separate ways of managing their
truststore - the file(s) they reference when validating a certificate. In Java,
this is typically a file called `cacerts`, which contains all the publicly
trusted CAs. This is file can be found in the distribution's `lib/security`
folder.

However, when using self-signed certificates, it's not always desirable to add
the custom root CA to the default `cacerts` file. In such cases, creating a
separate truststore is a simple and effective solution.

## Using keytool

Java comes bundled with a command-line tool called `keytool`, which is
essential for creating and managing a custom truststore.

To create a new truststore and add the provided root CA, use the following
command:

```shell
keytool -importcert -trustcacerts -file root-ca.crt -alias myCA -keystore my-truststore.jks -storepass change1t -noprompt
```

* `-importcert`: Configures keytool to import a certificate.
* `-trustcacerts`: Indicates that the certificate being imported is a certificate authority (CA).
* `-file`: Specifies the file path to the CA certificate (.crt).
* `-alias`: The internal name for the certificate within the truststore, used to distinguish it from others.
* `-keystore`: Specifies the file to create as a new truststore.
* `-storepass`: The password used to secure the truststore, preventing tampering.
* `-noprompt`: Automatically accepts the changes without prompting the user for confirmation.

You can use different file paths and alias names as needed. Be sure to choose a
strong `storepass`, especially when deploying to an external system - a weak
password could be exploited by an attacker if the system is compromised.

To list the certificates in the truststore, use this command:

```shell
keytool -list -keystore my-truststore.jks -storepass change1t
```

The output should look something like this:

```shell
Keystore type: PKCS12
Keystore provider: SUN

Your keystore contains 1 entry

myCAca, Oct 24, 2024, trustedCertEntry, 
Certificate fingerprint (SHA-256): 7A:71:63:2E:FC:6B:6F:3E:70:AD:28:FF:E8:E2:CB:AB:EE:36:BB:0F:01:68:B7:15:5A:F5:D8:62:BA:0A:78:98
```

## Summary

In this post, we've covered how to create and manage a custom Java truststore
using the `keytool` utility. This can be useful when dealing with self-signed
certificates generated for servers that require SSL/TSL validation.