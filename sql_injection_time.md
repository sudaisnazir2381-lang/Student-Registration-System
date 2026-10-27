# CVE-2026-18896: Time-Based SQL Injection in Student-Registration-System 1.0

| | |
|---|---|
| **CVE** | CVE-2026-18896 ([NVD](https://nvd.nist.gov/vuln/detail/CVE-2026-18896) · [VulDB](https://vuldb.com/cve/CVE-2026-18896)) |
| **Discovered by** | Sudais Nazir |
| **Published** | August 2026 |
| **Product** | [Student-Registration-System](https://github.com/lavkush-maurya/Student-Registration-System) (open-source PHP application) |
| **Affected version** | <= 1.0 |
| **Weakness** | CWE-89: SQL Injection |
| **Severity** | Medium, CVSS 3.1 base score 6.3 |
| **CVSS vector** | `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:L/I:L/A:L` *(confirm this matches the published CVE record)* |

---

## Summary

The `opwd` (old password) parameter in `student/changepass.php` is concatenated directly into an SQL query. An authenticated student can inject SQL and use time-based techniques to extract data from the database, including database and table names. No sanitization or prepared statements are used.

## Vulnerability Details

| Field | Value |
|---|---|
| **Type** | Time-based blind SQL injection |
| **Affected file** | `student/changepass.php` |
| **Affected URL** | `http://localhost/Student-Registration-System/student/changepass.php` |
| **Vulnerable parameter** | `opwd` (HTTP POST) |
| **Authentication required** | **[FILL: Yes, a logged-in student session / No]** |

## Root Cause

User input from `$_POST` is placed straight into the query string, so anything the user types becomes part of the SQL statement. The vulnerable pattern looks like this:

```php
$oldpass = $_POST['opwd'];
$query = "SELECT * FROM student WHERE spassword='$oldpass' AND ...";
```

The screenshot below shows the vulnerable code:

![Root cause: vulnerable code](https://github.com/user-attachments/assets/a57fea11-9314-4012-801f-e894a715714b)

Further code review evidence confirming that no validation or escaping is applied to the input:

![Code review: no input validation](https://github.com/user-attachments/assets/92974795-5f06-4158-b1b5-37fe9accd807)

## Proof of Concept

**Test environment:** PHP **[FILL version]**, MySQL/MariaDB **[FILL version]**, local XAMPP/LAMP install of Student-Registration-System 1.0.

### Steps to reproduce

1. Install Student-Registration-System 1.0 locally and log in as a student **[FILL: how you obtained a valid session]**.
2. Open the change-password page at `/student/changepass.php`.
3. Intercept the request with Burp Suite and send the following value in the `opwd` parameter:

   ```
   ' AND (SELECT 2713 FROM (SELECT(SLEEP(5)))pQmt) AND 'cuFH'='cuFH
   ```

4. Compare response times. A normal request returns in about **[FILL: e.g. 0.1] seconds**; the injected request is delayed by about **[FILL: e.g. 5] seconds**, which confirms the injected `SLEEP(5)` executed on the database.

Request showing the payload in `opwd`:

![Payload in the opwd parameter](https://github.com/user-attachments/assets/8675c96b-71c3-4fd9-a5ec-267408ae57e4)

Confirmation that `opwd` is injectable:

![Injection confirmed](https://github.com/user-attachments/assets/df97b822-cf45-486d-a200-a153271ee1ef)

### Data extraction

Using the same injection point, I extracted the database names and then the table names, showing that arbitrary data in the database can be read.

Database enumeration:

![Database names extracted](https://github.com/user-attachments/assets/9a50aab9-edb3-490b-a44a-c3b7f88dae95)

Table enumeration:

![Table names extracted](https://github.com/user-attachments/assets/7dc4713d-6671-4926-bf88-508a1420aef5)

## Impact

Through this injection point, a user can read data from the application's database, which includes other users' records and any stored credentials. Impact is limited to what the database account can access. Reading data was demonstrated; modification of data was not tested.

## Remediation

Use prepared statements so user input is never treated as SQL:

```php
$stmt = $conn->prepare("SELECT * FROM student WHERE spassword = ? AND sid = ?");
$stmt->bind_param("ss", $oldpass, $sid);
$stmt->execute();
$result = $stmt->get_result();
```

Additional hardening:

- Store passwords with `password_hash()` and check them with `password_verify()`, rather than comparing the password inside the SQL query.
- Run the application with a database account that has only the privileges it needs.
- Validate the type and length of all submitted fields.

## Evidence URLs

1. Root cause (vulnerable code): https://github.com/user-attachments/assets/a57fea11-9314-4012-801f-e894a715714b
2. Code review (no input validation): https://github.com/user-attachments/assets/92974795-5f06-4158-b1b5-37fe9accd807
3. Payload in the `opwd` parameter: https://github.com/user-attachments/assets/8675c96b-71c3-4fd9-a5ec-267408ae57e4
4. Injection confirmed: https://github.com/user-attachments/assets/df97b822-cf45-486d-a200-a153271ee1ef
5. Database names extracted: https://github.com/user-attachments/assets/9a50aab9-edb3-490b-a44a-c3b7f88dae95
6. Table names extracted: https://github.com/user-attachments/assets/7dc4713d-6671-4926-bf88-508a1420aef5

---

## Disclosure Timeline

| Date | Event |
|---|---|
| **06/15/2026** | Reported to VulDB |
| August 2026 | CVE-2026-18896 published |

## References

- [NVD: CVE-2026-18896](https://nvd.nist.gov/vuln/detail/CVE-2026-18896)
- [VulDB: CVE-2026-18896](https://vuldb.com/cve/CVE-2026-18896)
- [OWASP SQL Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)
- [CWE-89: SQL Injection](https://cwe.mitre.org/data/definitions/89.html)
- [PHP Manual: Prepared statements](https://www.php.net/manual/en/mysqli.quickstart.prepared-statements.php)
