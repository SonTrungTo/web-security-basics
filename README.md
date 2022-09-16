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
  - Implement Content Security Policy: states where javascript source are allowed.
    - `Content-Security-Policy: script-src 'self' https//...`
    - `Content-Security-Policy: script-src 'self'; report-uri https://example.com`
- Reflected XSS scripting attacks: inject malicious JS via HTTP requests.
  - Escape HTML characters.
  - More common in search page or error pages.
- DOM-based XSS scripting attacks: Attacks based on URI fragments. Only client and server cannot detect.
  - Escape HTMl characters.

## Cross-site request forgery attacks (CSRF/XSRF)
Clicking malicious link that generates unintended behaviours. \
`Example`: Previous GET requests on Twitter can create a post => unwanted behaviours.

**Solutions**:

  - Follow REST principles: GET should not change the state of a server.
Similarly, use POST -> modify an object, PUT -> create new object,
DELETE -> delete an object.
  - Implement anti-CSRF cookies: 
    - Users may be tricked into submitting a form/script in a 3rd
    party website controlled by the attacker.
    - Sensitive actions can be performed in reponse to such POST
    requests. We would like to ensure that such actions can only
    come from login requests on our site.
    - *anti-CSRF cookie*: randomized string written out to a named cookie parameter.
    - Server: `Set-Cookie: _xsrf=<random_string_A>` in res.header
    - Browser: `Cookie: _xsrf=<random_string_A>` in req.header
  