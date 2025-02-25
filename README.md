1. **Gunakan Parameterized Queries (Prepared Statements)**
   Alih-alih menyisipkan data langsung ke dalam query SQL, gunakan prepared statements dengan parameter. Ini memastikan bahwa input dianggap sebagai data, bukan bagian dari perintah SQL.

   Contoh pada PHP dengan MySQLi:
   ```php
   $conn = new mysqli("localhost", "user", "password", "database");
   $stmt = $conn->prepare("SELECT * FROM users WHERE username = ?");
   $stmt->bind_param("s", $username);
   $stmt->execute();
   ```

   Contoh pada Python dengan MySQL (MySQL Connector):
   ```python
   import mysql.connector
   conn = mysql.connector.connect(user='user', password='password', host='localhost', database='database')
   cursor = conn.cursor(prepared=True)
   query = "SELECT * FROM users WHERE username = %s"
   cursor.execute(query, (username,))
   ```

2. **Validasi dan Filter Input**
   Pastikan input hanya berisi karakter yang diizinkan. Misalnya, jika input hanya boleh berupa angka, gunakan filter untuk memastikan hal tersebut.

   Contoh Validasi di PHP:
   ```php
   if (!preg_match("/^[a-zA-Z0-9_]+$/", $username)) {
       die("Username tidak valid.");
   }
   ```

   Contoh Validasi di Python:
   ```python
   import re
   if not re.match("^[a-zA-Z0-9_]+$", username):
       raise ValueError("Username tidak valid.")
   ```

3. **Gunakan ORM (Object-Relational Mapping)**
   Gunakan ORM seperti SQLAlchemy (Python), Eloquent (Laravel), atau Hibernate (Java) untuk mencegah SQLi karena query dibuat secara otomatis tanpa manipulasi langsung string SQL.

   Contoh dengan SQLAlchemy (Python ORM):
   ```python
   user = session.query(User).filter_by(username=username).first()
   ```

   Contoh dengan Laravel Eloquent (PHP ORM):
   ```php
   $user = User::where('username', $username)->first();
   ```

4. **Batasi Hak Akses Database**
   Jangan gunakan akun database dengan hak akses root atau admin. Buat akun dengan hak akses terbatas untuk operasi tertentu.

   Contoh Membuat User dengan Hak Akses Terbatas di MySQL:
   ```sql
   CREATE USER 'limited_user'@'localhost' IDENTIFIED BY 'password123';
   GRANT SELECT, INSERT ON database_name.* TO 'limited_user'@'localhost';
   ```

5. **Sembunyikan Error Database**
   Jangan tampilkan error database ke pengguna karena bisa memberikan informasi sensitif.

   Contoh di PHP:
   ```php
   mysqli_report(MYSQLI_REPORT_OFF);
   ```

   Gunakan try-catch di Python:
   ```python
   try:
       cursor.execute(query)
   except mysql.connector.Error as err:
       print("Terjadi kesalahan.")
   ```

6. **Gunakan Web Application Firewall (WAF)**
   WAF seperti ModSecurity atau layanan cloud seperti Cloudflare WAF bisa memblokir serangan SQLi sebelum mencapai server.

   Untuk mengaktifkan ModSecurity di Apache:
   ```bash
   sudo apt install libapache2-mod-security2 -y
   sudo a2enmod security2
   sudo systemctl restart apache2
   ```

7. **Hindari Penggunaan Query Dinamis**
   Jangan buat query SQL dengan string yang digabung secara langsung. Misalnya, hindari ini:
   ```php
   $sql = "SELECT * FROM users WHERE username = '" . $_GET['username'] . "'";
   ```

   Sebaliknya, gunakan Prepared Statements seperti yang dijelaskan sebelumnya.

8. **Batasi Panjang Input**
   Setiap input pengguna harus memiliki batas panjang untuk mencegah payload panjang.

   Contoh di MySQL:
   ```sql
   CREATE TABLE users (
       username VARCHAR(50) NOT NULL
   );
   ```

   Contoh di PHP:
   ```php
   if (strlen($username) > 50) {
       die("Username terlalu panjang.");
   }
   ```

9. **Monitoring dan Logging**
   Aktifkan logging untuk mendeteksi pola serangan SQLi dan gunakan alat seperti Fail2Ban untuk memblokir IP yang mencurigakan.

   Mengaktifkan Logging Query di MySQL:
   ```sql
   SET GLOBAL log_output = 'FILE';
   SET GLOBAL general_log_file = '/var/log/mysql/general.log';
   SET GLOBAL general_log = 'ON';
   ```

Dengan menerapkan teknik di atas, aplikasi kamu akan jauh lebih aman dari serangan SQL Injection.
