## ONLINE BIDDING SYSTEM IN PHP WITH SOURCE CODE 

## supplier 

[Online Bidding System In PHP With Source Code - Source Code & Projects](https://code-projects.org/online-bidding-system-in-php-with-source-code/)

### Product and Version Affected

- **Product Name:** Online Bidding System In PHP With Source Code
- **Affected File: ** administrator/notif.php，administrator/functions.php

### Affected Code Snippets

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

administrator/notif.php

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

### **This is a similar question, you can take a look at this.**

[upload 1.md](https://github.com/Yohane-Mashiro/cve/blob/main/upload%201.md) 
