# Unrestricted File Uploads:
[Home](../../index.md) | [CheatSheets](../../cheatsheets.md) | [Theory](../../theory.md) | [About](../../about.md) | [Back](../webappvulns.md)

### What is a File Upload Vulnerability:
File uploads or upload functions in general provide quite a large attack surface for testers and adversaries alike, they are of particular interest from an offensive security perspective since upload functions by their very nature, allow us to potentially upload malicious content to the web application server. Developers are often keen to prevent file upload vulnerabilties from existing in their application since having one can result in catastrophy should an adversary discover one. Developers often enforce restrictions on the types of files that are uploaded and the content that makes up these files, failing to enforce proper restrictions without validating the file name, type, contents or size could result in an attacker gaining unauthorised access to the web server or damaging its contents. 

The impact of a file upload vulnerability depends on what the web application fails to validate correctly, for example, the developers may have enforced restrictions on the files name, size and type but might have forgotten to check the actual contents of the file uploaded. In the worst case scenario, the file type is not validated by the server, this might allow an attacker to upload executable code to the target web server in PHP or JSP files. If successful, they could obtain a web shell on the target and effectively gain full control over the web server. 

If the filename of the uploaded file is not validated, an attacker could overwrite critical files on the web servers local filesystem. On the other hand, failing to perform checks on the size of the uploaded file could cause denial-of-service for the web application should an attacker be able to fill the disk space with arbitrary files. 

For the reasons listed above, its fairly rare to discover file upload vulnerabilities in the wild since file restrictions are typically at the forefront of development. File upload vulnerabilities therefore usually arise due to misconfigurations in the way the developers implemented this functionality, for example, developers may utilise a blacklist of file types but fail to account for parsing discrepencies when checking the file extension. Ultimately, even robust validation measures may be applied inconsistently across the network of hosts and directories that form the website, resulting in discrepancies that can be exploited. 

When handling static files web servers can respond in different ways:

* If this file type is non-executable, such as an image or a static HTML page, the server may just send the file's contents to the client in an HTTP response.

* If the file type is executable, such as a PHP file, and the server is configured to execute files of this type, it will assign variables based on the headers and parameters in the HTTP request before running the script. The resulting output may then be sent to the client in an HTTP response.

* If the file type is executable, but the server is not configured to execute files of this type, it will generally respond with an error. However, in some cases, the contents of the file may still be served to the client as plain text. Such misconfigurations can occasionally be exploited to leak source code and other sensitive information.

The Content-Type response header may provide clues as to what kind of file the server thinks it has served. If this header hasn't been explicitly set by the application code, it normally contains the result of the file extension/MIME type mapping. 

When we have discovered a file upload vulnerability, we might want to try and obtain a web shell on the target server. A web shell allows us to execute arbitrary commands on the web server, effectively allowing us to control what the web server does. Web shells can be implemented in many different web based scripting languages for example PHP or JSP. Below are some PHP on-liners to make a vulnerable web server execute commands:

```
<?php echo file_get_contents('/home/carlos/secret'); ?>

<?php echo system($_GET['command']); ?>

```

### Flawed File Type Validation:
As mentioned, file upload functions without restrictions are very rare in most applications due to their nature, this does not mean they don't exist however and we can attempt to bypass these restrictions. Different web applications attempt to validate and sanitize file uploads in their own way and we can exploit these flaws to upload files to a target web server.

When submitting a HTML form, this action is often performed via a HTTP POST request. The request will likely contain a content type header usually set to "application/w-www-form-url-encoded". This is fine for text based forms such as login forms, address forms, etc but it is not suitable for sending big chunks of binary data such as a PDF document. In this case, the content type header is set to multipart/form-data. 

When we upload an image to a web server, the request typically contains many different parts such as the binary content of the image, description of the image, etc. The message body is split into separate oarts for each of the forms inputs, each part contains a "Content-Disposition" header which provides basic information about the input field it relates to. The Content-Type header may also be present which tells the server the MIME type of the data that was submitted. 

Web applications may attempt to validate file uploads by checking that this input-specific "Content-Type" header matches the expected MIME type. If the server only expects image files, it may only allow image/jpeg or image/png. Issues arise when the value of this header is implicitly trusted by the server, if no further verification and validation is performed on the contents of the file to check that it matches the supposed MIME type, we might be able to upload arbitrary files to the web server.

### Preventing File Execution is User Accessible Directories:
Preventing malicious file uploads is the first point of call but in the event that an attacker is able to circumvent the validation and verification functions, it is best to also take additional security measures to prevent files in user accessible directories from being executed. Servers generally only run scripts whose MIME type has been explicitly configured to execute, otherwise some kind of error message should be returned. This behaviour can be interesting as it may provide a way to leak source code but it prevents us from creating a web shell. 

This kind of configuration differs between directories, directories which contain user uploaded files should and will likely have stricter restrictions than other locations on the filesystem, if is possible to upload a script to a different directory not intended to contain user uploaded files, the server might execute it. Web servers often use the filename field in multipart/form-data requests to determine the name and location where the file should be saved, since we can modify this value we might try to upload it to a directory closer to the web root. We can do this by prepending "../" to the filename of the file we're trying to upload, this might result in the file being uploaded to the parent directory which we may also be able to access. We can attempt to encode the file navigation command using URL encoding and other tricks in an attempt to bypass the validation checks performed by the server. 


You should also note that even though you may send all of your requests to the same domain name, this often points to a reverse proxy server of some kind, such as a load balancer. Your requests will often be handled by additional servers behind the scenes, which may also be configured differently.

### Weak Blacklist of Dangerous file Types:
One of the more common ways of preventing malicious file uploads is by using a blacklist to block potentially malicious extensions such as .php and .jsp. Blacklisting is very hard to get right and often implemented incorrectly since blocking every file type that could potentially execute code is time consuming and prone to errors. As such, blacklists can often be bypassed using lesser known file types and alternative file extensions, e.g .php5, .shtml, etc

As mentioned, servers typically dont execute files unless they have been configured to do so, e.g before an Apache server can execute PHP files requested by a client, developers might have to add the following directives to their apache configuration file (/etc/apache2/apache2.conf):

```
LoadModule php_module /usr/lib/apache2/modules/libphp.so
AddType application/x-httpd-php .php
```

Servers also allow developers to creates special config files within individual directories in order to override or add to one or more of the global settings. Apache servers for example will load directory-specific configurations from the .htaccess file if one is present. In IIS web servers, the web.config file can be used to achieve the same result, e.g:

```
<staticContent>
    <mimeMap fileExtension=".json" mimeType="application/json" />
</staticContent>
```
Web servers use these configuration files when present but they are usually not allowed to be access using HTTP requests. It might also be possible to upload your own malicious configuration file and in this case even if the file extension is blacklisted, you might be able to force the server into mapping arbitrary, custom file extensions to an executable MIME type.

By uploading our own .htaccess file we can attempt to force the server to execute our malicious scripts using arbitrary extensions:

```
AddType application/x-httpd-php .pwn
```

```
-----------------------------343802486225708921932812508841

Content-Disposition: form-data; name="avatar"; filename="profilepic.pwn"
Content-Type: application/x-php5
<?php echo file_get_contents('/home/user/.ssh/id_rsa'); ?

GET /files/avatars/profilepic.pwn HTTP/1.1
```

### Obfuscating File Extensions:
Even extensive blacklists can still be potentially bypassed by using simple obfuscation techniques, a common example is a blacklist that prevents .php files from being uploaded but allows .pHp files through. If the application code that maps the file extension to a MIME type is NOT case sensitive this discrepancy allows you to sneak malicious PHP files past validation that may eventually be executed by the server.

Other ways to bypass file extension validation using obfuscations include:

* Providing multiple extensions: .php.jpg, .php.png
* Adding trailing characters: exploit.php. 
* Using URL encoding or double URL encoding for dots, forward slashes, backwards slashes, etc. If the value isn't decoded when validating the file extension but is later decoded server side, this can allow you to upload malicious files. E.g exploit%2Ephp
* Adding semi-colons or URL-encoded null byte characteres before the file extension. If validation is written in  high level language such as PHP or Java but the server processes the file using low level C/C++ based languages, this can cause discrepancies in what is treated at the end of the filena,e: exploit.jsp;.jpg or exploit.asp%00.jpg
* Using multibyte unicode characters, which may be converted to null bytes and does after unicode conversion or normalisation. Sequences such as xc0, x2E, xC4, xAE or xC0 xAE may be translated to x2E if the filename is parsed as a UTF-8 string but then converted to ASCII characters before being used in the path. 

Other defences involve stripping or replacing dangerous extensions to prevent the file from being executed. If this transformation isn't applied recursively you can position the prohibited string in such a way that removing it still leaves behind a valid file extension. E.g consider what happens when you strip '.php' from the following filename:

```
exploit.p.phphp
```

### Broken Validation of File Contents:
More secure web servers will perform validation on not just the file descriptors such as the MIME type, but also ensure that the contents of the file matches the Content-Type header specified. In the case of an image upload function, the server might try to verify intrinsic properties of an image such as its dimensions. A PHP script will not contain any dimensions at all and the server will deduce that the file is not an image. Files can also contain what are known as "magic bytes", these are found in the header and footer of the file and act as a fingerprint or signature to determine whether the contents matches the expected type. A JPEG file for example, always begins with the bytes FF D8 FF.

This is a more robust way of validating file types but it is not fool proof. Using special tools such as ExifToll we can create a polyglot JPEG file that contains malicious code within its metadata.

```
exiftool -Comment="<?php echo 'START ' . file_get_contents('/etc/passwd') . ' END'; ?>" <YOUR-INPUT-IMAGE>.jpg -o polyglot.php
```

### File Upload Race Conditions:
Modern application frameworks are resistant to the types of attacks described above, files are generally not uploaded to their intended destination on the filesystem. Files are usually uploaded to a temporary sandboxed directory first and the filename is randomized to avoid overwriting existing files. After this, file validation is performed on the temporary file and only then is it transfered to its destination. 

Developers have the option to implement their own file upload functionality independent of any framework, this is a complex process and as a result race conditions can be introduced that bypass the security restrictions. Some websites upload the file directly to the main filesystem and remove it again if it doesn't pass the validation, this behaviour is typical of ant-virus software checking for malware. The process may only take a few milliseconds but for a short time the file exists on the server, this means an attacker can potentially execute their script. 

These vulnerabilities are very subtle and finding them in a black-box test is very difficult unless source code is leaked.

Race conditions can also occur in functions that allow you to perform file uploads via a URL. The server has to fetch the file over the internet and create a local copy before it can perform any validation. As the file is being loaded over HTTP, developers are unable to use their frameworks built-in mechanisms for validation files and instead use their own processes for string and validating the file. For example, the file could be loaded into a temporary directory with a random name, in theory it should be impossible to exploit any race condition if they don't know the directory name but if pseudo-random functions are used such as PHP uniqid(), it could be possible to bruteforce the directory name. To make the attack easier, you can pad out the file using arbitrary bytes and have the payload positioned at the start, this means the file will take longer to process and reside on the server for longer.

### Other Ways to Achieve RCE with File Uploads:
Using JavaScript, we might be able to create stored XSS payloads on the web application. Due to same-origin policy restrictions these scripts will need to be served from the same origin that you uploaded it to, common attack vectors include usernames, profile names, comments, etc.

Another method of attacking an application using file uploads is exploiting parsers used by the application. If the uploaded file seems to be both stored and served it might be possible to obtain RCE via parsers such as XML-parsers. XXE injection attacks often arise due to flaws in how the application parses XML content. 

Another method to try is the HTTP method PUT. This HTTP method is rarely used due to its insecure nature but if it is present on a web application it might be worth attempting to upload a file using the PUT method. You can try sending OPTIONS requests to different endpoints to test for any that advertise support for the PUT method. 

### How to Prevent File Uploads:
* Check file extensions against a whitelist rather than a blacklist.
* Make sure the filename does not contain any substrings that could be interpreted as a directory or a traversal sequence(../) 
* Rename uploaded files to avoid collisions that may cause existing files to be overwritten
* Do not upload files to the servers permanent file system until the file has been completely validated. 
* Establish a framework for preprocessing file uploads rather than attempting to write custom validation code. 

