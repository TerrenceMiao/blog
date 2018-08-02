---
title: 'An bash, curl and awk example'
date: 2018-08-03 09:00:06
tags:
---

This is an example how in a bash script, curl and awk work together:

<script src="https://gist.github.com/TerrenceMiao/2a89c5941503eb25fe15b86a333fdc49.js"></script>

``` Bash
#!/bin/bash

## Call Session API and get Authorization bearer token
token=$(curl -D - -X POST \
    https://api.paradise.org/session/v1 \
    -H "Accept: application/json" \
    -H "Cache-Control: no-cache" \
    -H "Content-Type: application/json" \
    -d '{
        "username": "master@paradise.org",
        "password": "Password Go Go Go"
    }' | awk 'BEGIN {FS=": "}/^Authorization/{print $2}')

## Migrate each member into new culture pot
for memberId in $(cat member-ids)
do
    curl -D - -X POST \
        https://api.paradise.org/cultrue/v2/migrate \
        -H "Accept: application/json" \
        -H "Authorization: $token" \
        -H "Cache-Control: no-cache" \
        -H "Content-Type: application/json" \
        -H "organisation-id: $memberId" \
        -d "{}"

    echo ""    
    echo "---------------------------------------------------------------------------------------------------"    
    echo ""    
done
```