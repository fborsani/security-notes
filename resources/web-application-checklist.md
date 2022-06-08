# Web application checklist

* [ ] Check SSL certificates date and validity
* [ ] Check TSL and HTTPS are being used with a strong cypher
* [ ] Page Enumeration
  * [ ] 404 page does not disclose server/os/db information or version
  * [ ] .htacces phpinfo .config and default administration pages return 404 or 403
  * [ ] folder indexing should be disabled or protected by password
  * [ ] is it possible to find CVEs with the disclosed information?
* [ ] Session
  * [ ] Session token are generated and stored via secure cookie on browser
  * [ ] exiting or pressing back button actually invalidates the session
  * [ ] cookie-based session tokens have the `Secure`, `HttpOnly` and `SiteOnly` attributes set
* [ ] Input fields are sanitized
  * [ ] Inputs from text editors are sanitized
  * [ ] Mail addresses sent to mail servers are sanitized to prevent IMAP or SMTP code injection
  * [ ] check for SLQi: `'";-#`
  * [ ] check for RCE: `;|"&,<>`
  * [ ] check for XSS: `<script>alert("ok");</script>`
  * [ ] check for SSTI: `${{<%[%'"}}%\.`
* [ ] Request parameters are sanitized
  * [ ] parameters allow for RFI
  * [ ] parameters allow for local file disclosure
* [ ] Authentication Page
  * [ ] 2FA/OTP
    * [ ] 2FA cannot be bypassed by skipping to result page
    * [ ] 2FA page does not display additional information about the user (such as phone number or email)
  * [ ] Captcha
    * [ ] Captcha is ignored if the field is missing in the request
    * [ ] Captcha is ignored if the filed is present but empty
    * [ ] Captcha result is embedded in the page's source code
    * [ ] Captcha result can be reused across sessions
  * [ ] Login form discloses whether a user exists or not
  * [ ] Password reset link is disabled and cannot be reused by different users
  * [ ] Passwords
    * [ ] Site does not allow passwords with weak structure or known weak passwords such as "1234", "querty", "letmein"
    * [ ] passwords should be tested against top 1000 of rockyou or similar bruteforce collection
    * [ ] default user passwords are not in use (admin - admin and similar)
* [ ] File upload
  * [ ] files are properly checked against both their extension and magic bytes in order to prevent malicious uploads
  * [ ] file uploads should have a size limit
  * [ ] upload of archives should be avoided or checked for zip bombs
  * [ ] files upload folder should not be reachable by LFI or indexable to prevent RCE
  * [ ] application should disable upload of SVG files since they can lead to XSS
  * [ ] test malicious filenames &#x20;
    * [ ] ../../../etc/passwd.jpg
    * [ ] sleep(10)-- -.jpg
    * [ ] ; sleep 10;

