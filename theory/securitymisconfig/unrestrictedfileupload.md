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
