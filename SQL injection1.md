## BLOG_SITE_IN_PHP_WITH_SOURCE_CODE  add_category.php sql injection



## supplier 

[Blog Site In PHP With Source Code - Source Code & Projects](https://code-projects.org/blog-site-in-php-with-source-code/)

### Product and Version Affected



- **Product Name:** BLOG_SITE_IN_PHP_WITH_SOURCE_CODE
- **Affected File:** resources/functions/blog.php , add_category.php



###  Detailed Description

The vulnerability resides within the `category_exists` function in `resources/functions/blog.php`, which handles user input processed via `add_category.php`. Although the code attempts to sanitize user input (`$name` and `$field`) using the `mysql_real_escape_string()` function, this defense mechanism is insufficient and inherently flawed, particularly because the program relies on the **deprecated and unsafe `mysql_\*` extension**.

The code is vulnerable to **Wide-Byte Injection**. If the database connection is configured to use a multi-byte character set, such as **GBK** or **BIG5**, an attacker can prepend their malicious payload with a specific leading byte (e.g., `0xdf` in GBK).

When `mysql_real_escape_string()` encounters a single quote (`'`), it prefixes it with a backslash (`\`, or `0x5c`). The vulnerable database server, interpreting the payload as a multi-byte stream, combines the attacker's leading byte (`0xdf`) with the inserted backslash (`0x5c`) to form a new, valid, non-backslash multi-byte character. This effectively **consumes the backslash**, allowing the subsequent single quote to escape and close the SQL string prematurely, leading to injection.

### Affected Code Snippets

#### resources/functions/blog.php

```
function category_exists($field,$name){
    $name = mysql_real_escape_string($name); // Wide-Byte Injection risk
    $field = mysql_real_escape_string($field); // Column name injection risk
    
    $query = mysql_query("SELECT COUNT(1) FROM categories WHERE `{$field}` = '{$name}'");
    
    return(mysql_result($query,0) == 0)?false : true;
}
```

add_category.php

```
<?php
include_once('resources/init.php');
if(isset($_POST['name'])){
    $name = trim($_POST['name']);
    
    if(empty($name)){
        $error = 'You must submit the category name';
    }
    else if(category_exists('name',$name)){ 
        $error = 'That category already exists';
    } else if(strlen($name)> 24){
        $error = 'The category name only be up to 24 characters only';
    }

    if(!isset($error)){
        add_category($name);
        header("Location:add_category.php");
        die();
    }
}
```



### Impact

1. **Logic Bypass:** An attacker can provide a payload (e.g., `%df' OR 1=1 -- `) that causes the `category_exists` function to always return `true` or `false`, bypassing input validation checks in `add_category.php`.
2. **Data Manipulation:** If the injection is successful, the attacker can execute arbitrary SQL commands, including:
   - Reading, modifying, or deleting data in the `categories` table.
   - Potentially performing **stacked queries** (depending on server configuration) to compromise data in other tables, leading to full database compromise or integrity loss.