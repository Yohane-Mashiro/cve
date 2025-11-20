## ONLINE BIDDING SYSTEM IN PHP WITH SOURCE CODE login.php sql injection

## supplier 

[Online Bidding System In PHP With Source Code - Source Code & Projects](https://code-projects.org/online-bidding-system-in-php-with-source-code/)

### Product and Version Affected

- **Product Name:** Online Bidding System In PHP With Source Code
- **Affected File:** login.php

### Detailed Description

The login module in `login.php` fails to properly sanitize or use parameterized queries for user input (`$_POST['user']` and `$_POST['pass']`). The user-supplied variables are directly concatenated into the SQL query string. This creates a classic **SQL Injection (SQLi)** vulnerability, specifically a **Tautology-based Injection** risk, allowing attackers to manipulate the database query logic.

### Affected Code Snippets

login.php

```
<?php
	if(isset($_POST['login'])){
				if(isset($_POST['user'])) {
					if(isset($_POST['pass'])){			
						$username = $_POST['user'];	
						$pass = $_POST['pass'];
						$query = mysql_query("SELECT * FROM member WHERE userid = '$username' AND  password = '$pass'") or die (mysql_error());
						$user = mysql_fetch_array($query);	
						if($user['verification'] == 'yes'){
							$_SESSION['ID'] = $user['memberid'];
							$_SESSION['logged'] = $user['memberid'];
							$_SESSION['user'] = $username;
							/*header('Location: myaccount.php');*/
					?>
                        <script> location.replace("myaccount.php"); </script>	
```

### Impact

**Authentication Bypass:** An attacker can input a specially crafted string (e.g., `' OR '1'='1`) into the username or password field. This causes the SQL query's `WHERE` clause to always evaluate to true, allowing the attacker to bypass the authentication mechanism and log in as the first user in the database (potentially an administrator) without valid credentials.
