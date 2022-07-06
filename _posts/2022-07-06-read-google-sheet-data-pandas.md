---
layout: post
title: How to read Google Sheet data using Pandas
description: "How to read Google Sheet data using Pandas"
keywords: "Google Sheet, Pandas"
---

### Step 1: Get Sharing Link

1) Click "Share" to get sharing link
2) Make sure to change general raccess as "viewer" or "editor"
3) Copy link

Use this link as an example: https://docs.google.com/spreadsheets/d/13hki9hrYXyTzzoiLt_ZNmhmqgAo6qYDbs5gJajvfv_E/edit?usp=sharing
This link exist only for testing purpose.

### Step 2: Read using pandas

1) Remove all string starting from edit and beyond

Remove parts after last forward slash
```
eg: https://docs.google.com/spreadsheets/d/13hki9hrYXyTzzoiLt_ZNmhmqgAo6qYDbs5gJajvfv_E -> /edit?usp=sharing
```

2) Get sheet_id. Sheet id starts from forward slash after d

```
eg: https://docs.google.com/spreadsheets/d/ -> 13hki9hrYXyTzzoiLt_ZNmhmqgAo6qYDbs5gJajvfv_E
```

3) Read using pandas csv

```
df = pd.read_csv(f"https://docs.google.com/spreadsheets/d/{sheet_id}/export?format=csv")
```

The purpose of extracting sheet id is to show that sheet_id can vary but all other strings should be similar.
