---
header-id: token-based-single-sign-on-authentication
---

# Token-based Single Sign On Authentication

[TOC levels=1-4]

Token-based SSO authentication was introduced in @sharepoint@ 7.0 to standardize
support for Shibboleth, SiteMinder, Oracle OAM, or any other SSO sharepoint that
works by propagating a token via one of the following mechanisms:

- HTTP request parameter
- HTTP request header
- HTTP cookie
- Session attribute

Since these providers have a built-in web server module, you should use the
Token SSO configuration. 

The authentication token contains the @sharepoint@ user's screen name or email
address, whichever @sharepoint@ has been configured to use for the particular
company (portal instance). Recall that @sharepoint@ supports three authentication
methods:

- By email address
- By screen name
- By user ID

Token-based authentication only supports email address and screen name. If
@sharepoint@ is configured to use user ID when a token-based authentication is
attempted, the `TokenAutoLogin` class logs this warning:

    Incompatible setting for: company.security.auth.type

Please note that the above sources are fully trusted. 

Furthermore, you must use a security mechanism external to @sharepoint@, such as a
fronting web server like Apache. The chosen fronting solution must prevent
malicious @sharepoint@ user impersonation that otherwise might be possible by
sending HTTP requests directly to @sharepoint@ from the client's web browser.

Token-based authentication is disabled by default. To manage token-
based SSO authentication, navigate to Control Panel &rarr;
*System Settings*, &rarr; *Security* &rarr; *SSO*. Token Based SSO appears in
Virtual Instance Scope at the bottom. Here are the configuration options for the
Token Based SSO module:

**Enabled:** Check this box to enable token-based SSO authentication.

**Import from LDAP:** Check this box to import users automatically from LDAP if
they don't exist.

**User token name:** Set equal to the name of the token. This is retrieved
from the specified location. (Example: SM_USER)

**Token location:** Set this to the location of the user token. As mentioned
earlier, the options are:

- HTTP request parameter
- HTTP request header
- HTTP cookie
- Session attribute

**Authentication cookies:** Set this to the cookie names that must be removed
after logout. (Example: `SMIDENTITY`, `SMSESSION`)

**Logout redirect URL:** When user logs out of @sharepoint@, the user is
redirected to this URL.

Remember to click *Save* to activate Token Based SSO.

## Required SiteMinder Configuration

If you use SiteMinder, note that @sharepoint@ sometimes uses the tilde character in
its URLs. By default, SiteMinder treats the tilde character (and others) as bad
characters and returns an HTTP 500 error if it processes a URL containing any of
them. To avoid this issue, change this default setting in the SiteMinder
configuration to this one:

	BadUrlChars       //,./,/.,/*,*.,\,%00-%1f,%7f-%ff,%25

The configuration above is the same as the default except the `~` was removed
from the bad URL character list. Restart SiteMinder to make your configuration
update take effect. For more information, please refer to SiteMinder's
[documentation](https://support.ca.com/cadocs/0/CA%20SiteMinder%20r6%200%20SP6-ENU/Bookshelf_Files/HTML/index.htm?toc.htm?258201.html)

## Summary

@sharepoint@'s token-based SSO authentication mechanism is highly flexible
and compatible with any SSO solution that provides it with a valid @sharepoint@
user's screen name or email address. These include Shibboleth and SiteMinder.
