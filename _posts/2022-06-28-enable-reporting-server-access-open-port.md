---
layout: post
title: Enable outside access to Power BI Reporting Server
description: "Enable others to access Power BI Reporting Server"
keywords: "Windows 10 2016 Server, Power BI Reporting Server"
---

When a Power BI Reporting Server is created and started, by default it will only be available to the machine itself. This is because Windows machine does not enable any machine to communicate to local ports. i.e. ports are not exposed.

The report server should work before proceeding to open the port. By default, server is available on port 80. So, only port 80 is exposed. This is done by adding a rule in Windows Firewall.

1. Search "Windows Firewall with Advance Security Settings" for Windows Server 2016.
2. Right click "Inbound Rules"
3. Click "New Rule"
4. For rule type, choose "Port" then click "Next"
5. Select "TCP"
6. Enter port number - 80 in the "Specific local ports" options then click "Next"
7. Choose "Allow All Connection" then click next
8. Choose profile type accordingly. Choosing all opens the port to anyone. Then click "Next"
9. Put a name you can remember and good indication. You can put "ReportServer (TCP on port 80)". Add description if needed.
10. Click finish.

If it doesn't work right away, restart the machine.
