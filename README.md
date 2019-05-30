### Contents

- [Objective of Attack](#objective-of-attack)
- [Vulnerabilities](#vulnerabilities)
  - [File Upoad Vulnerability](#file-upoad-vulnerability)
  - [Code Execution Vulnerability](#code-execution-vulnerability)
  - [File Inclusion Vulnerability](#file-inclusion-vulnerability)
  - [SQL Injection Vulnerability](#sql-injection-vulnerability)
- [References](#references)

## Objective of Attack

1. System takeover - Gain access to server

2. Overloaded filesystem - By uploading large files

## Vulnerabilities

### File Upoad Vulnerability

**Potential Attacks**

1. Upload an executable which is used to gain reverse shell access to the server

2. Upload large files incurring storage and bandwidth costs

3. Overwrite a critical file on the server

**Mitigation**

1. Enforce both client side and server side checks. Since client side checks can be overriden by using a proxy, it is essential to enforce validation on server side

2. Enforce a file size limit on uploaded files

3. Validate file for expected content
   1. Via MIME Type
   2. Via file extension
   3. Check the content of the files using a helper library for the expected type

4. Rename the uploaded file
   1. The metadata of the uploaded file is used to decide the name and path of the stored file. 
   2. Check for path injection in the name of the file
   3. For images explore if you can recreate the image from the uploaded image before storing

5. File use on Server - Outline the flow of how an uploaded file is being used by your server. Identify points of injection and setup filters accordingly

**Node.js**

1. Check if the files are being stored in memory or on-disk
   
   Uploading very large files, or relatively small files in large numbers very quickly, can cause your application to run out of memory when memory storage is used

2. Don't add `multer` (NPM module to upload files) as a global middleware since a malicious user could upload files to a route that you didn't anticipate. Use `multer` only on routes where you are handling the uploaded files

### Code Execution Vulnerability

Allow an attacker to execute OS commands and can be used to gain reverse shell access

**Mitigation**

1. Avoid allowing users to run any sort of code on your server
   
   In Node.js try to avoid functions such as `eval` etc that allow a user to run OS code on the server

### File Inclusion Vulnerability

Allows attacker to read any file on the server. This allows access to configuration files and files storing credentials

**Potential Attacks**

This vulnerability can be escalated by an attacker to inject code into a readable file (eg. log file) and then have the code executed (I was able to find examples for PHP but not for Express/ Node.js where File Inclusion vulnerability can be escalated to execute code on server side)

**Mitigation**

When using Express, ensure to filter file path input when using the `sendFile` method. It is recommended to use `express.static` middleware as it filters for file path injection.

Refer [Github - local-file-inclusion](https://github.com/dsinecos/local-file-inclusion) for a demonstration

**Remote File Inclusion**

Is similar to Local File Inclusion except the exploited files are accessed on a different machine/ server within the same network as the target website


## References

[Learn Website Hacking / Penetration Testing From Scratch](https://www.udemy.com/course/learn-website-hacking-penetration-testing-from-scratch/)

[Node.js Security Roadmap](https://github.com/google/node-sec-roadmap)