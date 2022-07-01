---
layout: post
title: Adding a user to Local Administrator group
description: "Adding a user to Local Administrator group"
keywords: "Windows 10 2016 Server, add user group, Local Administrator"
---

## Step 1: Add new User in Computer Management

1. Search > Computer Management
2. Go to Local User and Groups
3. Right Click "Users"
4. Add New User and all related fields. Set password behavior accordingly. For username with domain enter username without domain.
eg: For DOMAIN/myname only enter myname
5. Create user

## Step 2: Add user in Groups

1. Continue step 1 after creating user. Go to Groups
2. Right click "Administrator"
3. Click "Add"
4. In the "Enter Object Names ... " section enter previous registered user
eh: myname
5. Click "Check Names"
6. Windows will promt Login - Use password same as windows login
7. Click "Ok"
8. For the changes to take effect, user need to sign out from computer (or VM server) and re-login again.
