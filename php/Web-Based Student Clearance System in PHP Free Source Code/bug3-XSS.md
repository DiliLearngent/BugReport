# [Web-Based Student Clearance System in PHP Free Source Code-XSS vulnerabilities-add-student.php]

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

**[Vulnerability type]:** Stored XSS

**[Vulnerability Url]:** http://10.142.221.69/student_clearance_system_Aurthur_Javis/admin/add-student.php

**[Vulnerability Location]:** student_clearance_system_Aurthur_Javis/Admin/add-student.php

**[Vulnerability Test]:**

1. Add a student, the Fullname is `<script>alert(1)</script>`, after the addition is successful, a pop-up window will appear on the page

   ![](..\..\img\20230330135401.png)

   ![](..\..\img\20230330135525.png)

   ![](..\..\img\20230330135603.png)


**[code analysis]:**

In student_clearance_system_Aurthur_Javis/Admin/add-student.php

```php
if(isset($_POST["btnregister"]))
{


    $permitted_chars = '0123456789abcdefghijklmnopqrstuvwxyz';
    $password_stud = substr(str_shuffle($permitted_chars), 0, 6);

    $fullname = mysqli_real_escape_string($conn,$_POST['txtfullname']);
    $matric_no = mysqli_real_escape_string($conn,$_POST['txtmatric_no']);
    $phone = mysqli_real_escape_string($conn,$_POST['txtphone']);
    $session = mysqli_real_escape_string($conn,$_POST['cmdsession']);
    $faculty = mysqli_real_escape_string($conn,$_POST['cmdfaculty']);
    $dept = mysqli_real_escape_string($conn,$_POST['cmddept']);
    $phone = mysqli_real_escape_string($conn,$_POST['txtphone']);


    $sql = "SELECT * FROM students where matric_no='$matric_no'";
    $result = mysqli_query($conn, $sql);

    if (mysqli_num_rows($result) > 0) {
        $_SESSION['error'] =' Matric No already Exist ';

    }else{
        //save users details
        $query = "INSERT into `students` (fullname,matric_no,password,session,faculty,dept,phone,photo)
VALUES ('$fullname','$matric_no','$password_stud','$session','$faculty','$dept','$phone','uploads/avatar_nick.png')";


        $result = mysqli_query($conn,$query);
        if($result){
                $_SESSION['matric_no']=$matric_no;

                //SEnd password Via SMS
                $username='rexrolex0@gmail.com';//Note: urlencodemust be added forusernameand 
                $password='admin123';// passwordas encryption code for security purpose.

                $sender='AUTHUR-JAVI';
                $message  = 'Dear '.$fullname.', Your password for online clearance system is :'.$password_stud.' ';
                $api_url  = 'https://portal.nigeriabulksms.com/api/';

                //Create the message data
                $data = array('username'=>$username, 'password'=>$password, 'sender'=>$sender, 'message'=>$message, 'mobiles'=>$phone);
                //URL encode the message data
                $data = http_build_query($data);
                //Send the message
                $request = $api_url.'?'.$data;
                $result  = file_get_contents($request);
                $result  = json_decode($result);


                $_SESSION['success'] ='Student Registration was successful';

		}else{
			$_SESSION['error'] ='Problem registering student';

		}
	}
}
```

After the fullname gets the value, it is directly stored in the database without any filtering

At the same time, the database data is queried and directly echoed in many pages, such as student-record.php, which leads to XSS

```php+HTML
<div class="user-panel mt-3 pb-3 mb-3 d-flex">
        <div class="image">
          <img src="../<?php echo $row['photo'];  ?>" alt="User Image" width="220" height="192" class="img-circle elevation-2">        </div>
        <div class="info">
          <a href="#" class="d-block"><?php echo $row['fullname'];  ?></a>
        </div>
      </div>
```

