# Vulnerabilities
## Injection
Injection is about inserting external codes to application in order to take
control of the app or read sensitive data.
- SQL Injection
  - Parameterized statements.
  - ORM approach.
  - Principles of least privilege.
  - Blind vs non-blind SQL.
- Command injection
  - Escape control characters
- Remote code execution
  - Serialization: converting data structure to binary data.
  - Deserialization: converting that binary data back to the data structure.
  - Beware of security advises for serialized libraries.
- File upload vulnerability
  - Host file on a secured service (AWS S3, AWS CDN, ...).
  - Make sure uploaded file is not executable.
  - Validate content of uploaded file.
  - Run anti-virus software.

## Cross-site (XSS) scripting attacks
Injection to browser.
- Stored XSS scripting attacks: JS is written from DB and browser executes.
  - Escape HTML characters: control entity -> entity encoding (e.g, `&` => `&amp;`)
  - Implement Content Security Policy.
- 