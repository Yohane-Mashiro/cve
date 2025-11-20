## BLOG_SITE_IN_PHP_WITH_SOURCE_CODE admin.php Unauthorized

## supplier 

[Blog Site In PHP With Source Code - Source Code & Projects](https://code-projects.org/blog-site-in-php-with-source-code/)

### Vulnerability Summary

**Unauthorized access to the sensitive `admin.php` page, allowing any user to perform administrative operations or view sensitive information without prior authentication.**



### Product and Version Affected



- **Product Name:** BLOG_SITE_IN_PHP_WITH_SOURCE_CODE
- **Affected File:** admin.php



### Detailed Description



The `admin.php` file serves as the central administration panel for the blog system, typically used for publishing, editing, and deleting posts, managing comments, and configuring system settings.

The root cause of this vulnerability is the **lack of proper authentication and authorization checks within the `admin.php` file**. An attacker can directly access this file via its URL, such as `http://[site]/admin.php`, without the system verifying if the requesting user is logged in or possesses administrative privileges.

This can lead to the following consequences:

1. An unauthorized user can directly view the administrative dashboard.
2. An unauthorized user can call administrative functions defined within `admin.php` or its included files (e.g., via crafted HTTP GET/POST requests), potentially leading to data leakage, system configuration changes, or content compromise.

![image-20251120211347856](./Unauthorized.assets/image-20251120211347856.png)

### Suggested Fixes



1. **Implement Strict Session and Authentication Checks:** At the **very beginning** of the `admin.php` file, mandatory logic must be added to check if the user is logged in and if their session is valid.
2. **Implement Authorization Checks:** Before executing any sensitive operations, verify that the logged-in user possesses the required **administrator role** to perform that action.