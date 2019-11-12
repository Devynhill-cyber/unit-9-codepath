# unit-9-codepath
Pentesting Live Targets

Time spent: 6 hours spent in total

> Objective: Identify vulnerabilities in three different versions of the Globitek website: blue, green, and red.

The six possible exploits are:
* Username Enumeration
* Insecure Direct Object Reference (IDOR)
* SQL Injection (SQLi)
* Cross-Site Scripting (XSS)
* Cross-Site Request Forgery (CSRF)
* Session Hijacking/Fixation

Each version of the site has been given two of the six vulnerabilities. (In other words, all six of the exploits should be assignable to one of the sites.)

# Red

**Vulnerability #1: Insecure Direct Object Reference (IDOR) **

The red site is vulnerable to an IDOR attack, as inactive salespeople's detail pages are accessible by altering the value of the `id` GET parameter in the URL `/red/public/salesperson.php?id=`. 

![](IDOR.gif)

**Vulnerability #2: Cross-Site Request Forgery (CSRF) **

The red site does not require a valid CSRF token to be submitted when updating a salesperson's information via the URL `/red/public/staff/salespeople/edit.php?id=`, where `id` is the salesperson's ID. This enables covert editing of data through hiding requests to this endpoint in other pages an authenticated user might visit. 

![](CSRF.gif)



# Blue

**Vulnerability #1: SQL Injection **

The blue site is vulnerable to SQL injection on the path `/blue/public/staff/salespeople/show.php?id=`, where the `id` GET variable can be used as an injection point. 

setting `id` to the value:
```mysql
' AND 0 UNION SELECT 1,2,3,4,GROUP_CONCAT(table_name,0x2e,column_name,"\n") FROM information_schema.columns WHERE table_schema=database(); -- 
```
allows the attacker to obtain details about the table and columns in the database. (Note: the above value should be URL encoded if appended directly to the URL.)

![](SQLi.gif)

**Vulnerability #2: Session Hijacking/Fixation **

The blue site is vulnerable to session fixation and hijacking attacks. Using the supplied `public/hacktools/change_session_id.php` tool, a session created in one browser was successfully used in a different.

![](SHF.gif)

# Green

**Vulnerability #1: Username Enumeration **

The green site's login mechanism leaks information which indicates if a username exists in the database. If a username is not found in the database, the following HTML is returned:
```HTML
<span class="failed">Log in was unsuccessful.</span>
```
If a username is found in the database, an invalid login attempt returns the following HTML:
```HTML
<span class="failure">Log in was unsuccessful.</span>
```
This allows an attacker to identify valid usernames through brute force submissions to the login page.

![](UE.gif)

**Vulnerability #2: Cross-Site Scripting **

The green site's contact form (found at `/green/public/contact.php`) allows arbitrary JavaScript to be submitted and later executed by authenticated users in the administration interface (found at `/green/public/staff/feedback/index.php`).

For example, submitting the following JavaScript in the "Feedback" field of the contact form will lead to a JavaScript alert appearing when feedback is viewed by an authenticated user.

```HTML
<script>alert('zmh68 - XSS');</script>
```
![](XSS.gif)
