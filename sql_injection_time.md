# Title: [sql-injection] in [Student-Registration-System] <= [v1.0]

**BUG Author:** Sudais Nazir

---

# Product Information

- **Software Link:** https://github.com/lavkush-maurya/Student-Registration-System
- **Affected Version:** <= v1.0
- **BUG Author:** Sudais Nazir

---

# Vulnerability Details

| Field | Value |
|---------|---------|
| **Type** | Time Based SQL Injection |
| **Affected URL** | `http://localhost/Student-Registration-System/student/changepass.php` |
| **Vulnerable Parameter** | `opwd` |

## Vulnerable Files

| File Name | Path |
|------------|------|
| `changepass.php` | `Student-Registration-System/student/changepass.php` |

---

# Vulnerability Type

- **CWE:** CWE-89 (SQL Injection)
- **Severity:** CRITICAL
- **CVSS Score:** 9.1

---

# Root Cause

The code directly inserts user-provided data from `$_POST` (e.g., `spassword='$oldpass'`) into SQL queries without sanitization, allowing attackers to manipulate queries.

## Screenshot

![Root Cause Screenshot](https://github.com/user-attachments/assets/a57fea11-9314-4012-801f-e894a715714b)

**Direct Link:**  
https://github.com/user-attachments/assets/a57fea11-9314-4012-801f-e894a715714b

---

# Impact

- Unauthorized access to database information.
- Potential exposure of sensitive information (such as user passwords).
- Possible database corruption or data manipulation.

---

# Description

## 1. Vulnerability Details

- In this PHP code, the `oldpass` parameter is directly concatenated into SQL Statement.
- No input validation or escaping mechanisms implemented.

## 2. Attack Vectors

- Attackers can manipulate SQL query structure using special characters.
- Additional information can be extracted using Time Based Payloads.
- Database information can be obtained through Time Based injection.
- Time based injection might reveal more information.

## 3. Attack Payload Examples

### SQL Payload

```sql
' AND (SELECT 2713 FROM (SELECT(SLEEP(5)))pQmt) AND 'cuFH'='cuFH
```

### Screenshot

![Payload Screenshot](https://github.com/user-attachments/assets/8675c96b-71c3-4fd9-a5ec-267408ae57e4)

**Direct Link:**  
https://github.com/user-attachments/assets/8675c96b-71c3-4fd9-a5ec-267408ae57e4

---

# Code Scan

## Scanner

https://github.com/sudaisnazir2381-lang

### Findings

The code scan found that there is no input validation or escaping in the `changepass.php` file.

### Screenshot

![Code Scan Screenshot](https://github.com/user-attachments/assets/92974795-5f06-4158-b1b5-37fe9accd807)

**Direct Link:**  
https://github.com/user-attachments/assets/92974795-5f06-4158-b1b5-37fe9accd807

---

# Proof of Concept

## Information Extraction

### Payload

```sql
' AND (SELECT 2713 FROM (SELECT(SLEEP(5)))pQmt) AND 'cuFH'='cuFH
```

### Result

`opwd` is injectable.

### Screenshot

![Injection Proof](https://github.com/user-attachments/assets/df97b822-cf45-486d-a200-a153271ee1ef)

**Direct Link:**  
https://github.com/user-attachments/assets/df97b822-cf45-486d-a200-a153271ee1ef

---

## Databases Information Extracted

![Database Enumeration](https://github.com/user-attachments/assets/9a50aab9-edb3-490b-a44a-c3b7f88dae95)

**Direct Link:**  
https://github.com/user-attachments/assets/9a50aab9-edb3-490b-a44a-c3b7f88dae95

---

## Tables Information Extracted

![Table Enumeration](https://github.com/user-attachments/assets/7dc4713d-6671-4926-bf88-508a1420aef5)

**Direct Link:**  
https://github.com/user-attachments/assets/7dc4713d-6671-4926-bf88-508a1420aef5

---

# Evidence URLs

1. https://github.com/user-attachments/assets/a57fea11-9314-4012-801f-e894a715714b
2. https://github.com/user-attachments/assets/8675c96b-71c3-4fd9-a5ec-267408ae57e4
3. https://github.com/user-attachments/assets/92974795-5f06-4158-b1b5-37fe9accd807
4. https://github.com/user-attachments/assets/df97b822-cf45-486d-a200-a153271ee1ef
5. https://github.com/user-attachments/assets/9a50aab9-edb3-490b-a44a-c3b7f88dae95
6. https://github.com/user-attachments/assets/7dc4713d-6671-4926-bf88-508a1420aef5

---

# Suggested Remediation

- Implement Prepared Statements.
- Input Validation.

---

# Security Recommendations

- Implement principle of least privilege.
- Encrypt sensitive data storage.
- Implement WAF protection.
- Conduct regular security audits.
- Use ORM frameworks for database operations.

---

# Additional Information

- Refer to the OWASP SQL Injection Prevention Guide.
- Consider using modern frameworks like MyBatis or Hibernate.
- Implement logging and monitoring mechanisms.

---

# References

- OWASP SQL Injection Prevention Cheat Sheet
- CWE-89: SQL Injection
- CERT Oracle Secure Coding Standard for Java

---

# Severity Assessment

> The severity of this vulnerability is HIGH, and immediate remediation is recommended as it poses a serious threat to the system's data security.

---

# Mitigation Timeline

| Timeline | Action |
|-----------|---------|
| **Immediate** | Implement prepared statements |
| **Short-term** | Add input validation |
| **Long-term** | Consider migrating to an ORM framework |

---

# Conclusion

This vulnerability requires immediate attention due to its potential for significant data breach and system compromise.
