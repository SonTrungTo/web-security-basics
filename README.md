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
  - SameSite Cookie attribute.
    - By default, any requests coming to site A will be attached a
    last known cookie.
    - Malicious 3rd party website can use this cookie (stolen) to
    create a CSRF attack.
    - `Set-Cookie: _xsrf=<random_string_A>; SameSite=strict` => strip
    any cookie from external sources (not from site A).
    - Would be painful if anytime someone shares a video, you have to 
    login back to your site A.
    - `Set-Cookie: _xsrf=<random_string_A>; SameSite=lax` => only GET
    requests are allowed to send cookies from another site.
  - Bonus: Require Re-authentication for sensitive actions.
    - Like when you are about to pay money via online bank accounts.

## Compromising Authentication
Breaking your logins.

**Solutions**
  - Use third-party authentication.
    - Examples: OAuth(Open Authentication) or OpenID/SAML. The first
    is for an application login with multiple endpoints authorized
    (controlled) while the latter is for federated authentication,
    a single login for multiple applications.
  - Secure your own authentication system.
    - Emails generated by a site (in response to a user's action)
    is called *transactional emails*. Sending those to an unverified
    address will be blocked by ISP for spamming.
    - Need to verify such address by checking DNS for MX record.
    - Beware/Banning disposable email accounts.
    - Securing password-resets: should be short-lived and expired
    after a user has used them.
    - Require complex password.
    - Securely store password:
      - Do not store passwords in *cleartext* storage (as is). Use
        1. Cryptographic hash algorithm => store *hash values*. A
        good library to use is `bcrypt`.
        2. Salting hashses, adding elements of randomness and avoiding
        the use of *rainbow tables*, exposed *hashed values* for a
        known hash algorithm.
    - Requiring Multifactor Authentication (MFA)
      - Requires a returning user to have *at least* two of the
      followings:
        1. Something they know.
        2. Something they have.
        3. Something they are.
    - Securing the logout
      - Logging out should clear session cookie in the browser *and*
      invalidate the session identifier on the server side.
    - Preventing user enumeration
      - Keep the messages generic.
      - Implementing CAPTCHA (Completely Automated Public Turing Test
      to tell Computer and Human Apart).

## Session Hijacking
A *session* is a conversation between browser and the server via HTTP
requests of an authenticated user.

A hacker can steal someone else's valid session while it's in progress.
This is called *session hijacking*.

There are 3 ways:
  - **Cookie theft**
    - Server-side session: session state stored in memory(cache)/
    database from the server => scalability problem.
    - Client-side session: session state stored in the browser
    => security issue: either encrypt session state before sending
    back to the client or digitally sign the session state.
    - To prevent, `Set-Cookie: session_state=<...>; HttpOnly; Secure;
    SameSite=Lax` returned by the server.
      - `HttpOnly` => prevent JS script from reading `Cookie` header.
      - `Secure` => prevent *man-in-the-middle* attack, sniffing
      traffics between browser and the server.
      - `SameSite=Lax` => prevent CSRF attack, an indirect interaction
      on your site by the hacker.
  - **Session fixation**
    - For legacy websites.
    - An attacker attach a dummy sessionId on the URL and send the link
    with that sessionId to a user, who authenticate the link, and now
    the dummy sessionId becomes the real sessionId.
  - **Weak session IDs**
    - Brute-force attack on sessionId.
    - Guess the seeds value. Rarely a random number is generated
    in software development.
    - The seeds value ranges from system-clock to some hashed object
    => use strong generated-random value.

## Permission

Facebook's 2018 debacle: hacker exploits a bug in video uploading and
generates access tokens to compromise nearly 50 millions users'
accounts.

  - *Privilege escalation*: a malicious user usurps the permission of
  another user.
    - *Vertical escalation*: a hacker tries to access root's permission
    => usurp the user's permission. (e.g, by installing *web shell* on
    an OS in which a web server is running)
    - *Horizontal escalation*: access another account with similar 
    privilege. Similar to Facebook's debacle, an API
    that grants access tokens without checking user's permission.
    Others including: guessing passwords, session hijacking,
    maliciously crafted HTTP requests.

**Solutions**:
### Access Control
The process of securing users to correctly identify them with 
permissions.

  - **Authentication**: Correctly identify a user when he/she returns 
  to the site.
  - **Authorization**: Assign to the user what he/she are allowed and
  not allowed to do.
  - **Permission checking**: Check these permissions every time
  a user performs a sensitive action.

A good access control includes:
  1. Choosing an authorization model
      - **Access Control Lists(ACLs)**: 
  2. Implementing an access control
  3. Testing the access control
  4. Implementing audit trails
  5. Avoiding common oversights
