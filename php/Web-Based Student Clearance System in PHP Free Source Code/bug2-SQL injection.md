# [Web-Based Student Clearance System in PHP Free Source Code-SQL injection vulnerabilities-Admin-login.php]

## Basic Information

**[Bug Reporter]:**DililLearngent

**[Application Vendors]:**https://www.sourcecodester.com/php/15627/web-based-student-clearance-system.html

**[Test Environment]:** windows10+apache+php7.0

**[Install]:**

1. start ***\*Apache\**** and ***\*MySQL\****.

2. **Extract** the **downloaded source code \**zip\** file**.

3. **Copy** the extracted source code folder and **paste** it into the **www directory**.

4. **Create** a **new database** naming **student_clearance**.

5. **Import** the provided **SQL** file. The file is known as **student_clearance.sql** located inside the **db** folder.

6. **Browse** the **Student Clearance System** in a **browser**. 

   i.e. **http://localhost/student_clearance_system_Aurthur_Javis/** for students 

   and **http://localhost/student_clearance_system_Aurthur_Javis/admin** for the admin panel.

   Admin Login：Username: **admin**      Password: **admin123**

## Bug Information

**[Vulnerability type]:** SQL injection vulnerabilities

**[Vulnerability Url]:** http://10.142.221.69/student_clearance_system_Aurthur_Javis/admin/login.php

**[Vulnerability Location]:** student_clearance_system_Aurthur_Javis/Admin/login.php

**[Vulnerability Test]:**

1. Test whether there is SQL injection in the login interface, the user name is 1' or 1=1#, the password is any value, and the login is successful after the test

   ![](..\..\img\20230330133945.png)

   ![](..\..\img\20230330134206.png)

2. If the user name uses -1' or 1=2#, and the password is arbitrary, the login will fail

   ![](..\..\img\20230330134322.png)

3. Therefore, two different page responses will be returned according to whether the condition behind or is true or not. There is a Boolean blind note, and the relevant content in the database can be blasted by constructing scripts


**[code analysis]:**

In student_clearance_system_Aurthur_Javis/Admin/login.php:

```php
if(isset($_POST['btnlogin']))
{

    $username = $_POST['txtusername'];
    $password = $_POST['txtpassword'];
    $status = 'Active';


    $sql = "SELECT * FROM admin WHERE username='" .$username . "' and password = '". $password."' and status = '". $status."'";
    $result = mysqli_query($conn,$sql);
    $row  = mysqli_fetch_array($result);

    $_SESSION["admin-username"] = $row['username'];

    $count=mysqli_num_rows($result);
    if(isset($_SESSION["admin-username"])) {
        {       

            header("Location: index.php"); 
        }
	}
	else { 
		$_SESSION['error']=' Wrong Username and Password';
    }
}
```

The obtained username and password are not filtered by any keywords, and the constructed SQL statement is simply concatenated, so there is SQL injection



