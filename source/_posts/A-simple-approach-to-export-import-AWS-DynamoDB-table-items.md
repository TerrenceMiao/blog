---
title: A simple approach to export / import AWS DynamoDB table items
date: 2019-04-23 18:59:29
tags:
---

Try to export items from AWS Test environment DynamoDB tables into Production. A simple Bash SHELL script, with a few commands and AWS CLI could do the work.

- In AWS DynamoDB console, export selected items into .csv file like this:

```console
𝜆 cat dws-tile-publisher-ptest-tile.csv
"id (S)","available_to (SS)","benefit_type (S)","category_id (S)","created_date (S)","created_user (S)","description (S)","image_bucket (S)","image_key (S)","last_modified_date (S)","last_modified_user (S)","status (S)","sub_category_id (S)","title (S)","valid_from (S)","valid_to (S)","claim_url (S)","discount_code (S)"
"950c529b-d6ae-472b-b44a-510ec201c167","{ ""0b1b9ed4-e171-4eaf-9d20-1a870ab7cc7c"", ""2df9daf4-da50-4ff9-9322-969d02c178f4"", ""b6fdcdc7-ffe6-4007-ae56-f5ab545768ec"", ""bc1c7285-ac47-4d06-b4c6-6f3780cbfb3f"" } ","DISCOUNT","8d27fb2d-c5f8-487a-a869-2ffd0e476eb6","2019-04-23T01:02:18.123","Angelo.Woods@test.npe.paradise.org","Testing descriptions","dws-tile-images-ptest","Samsung-Galaxy-10-Leak.jpg","2019-04-23T01:02:18.125","Angelo.Woods@test.npe.paradise.org","ARCHIVED","6936a6ed-fa97-4439-9129-c43e288011b3","Samsung Galaxy 10","2019-03-24T01:00","2021-02-25T23:00","http://www.google.com",

...

"095cc9b4-fd64-4479-8817-0b35b8ddcbc2","{ ""0b1b9ed4-e171-4eaf-9d20-1a870ab7cc7c"", ""2df9daf4-da50-4ff9-9322-969d02c178f4"", ""b6fdcdc7-ffe6-4007-ae56-f5ab545768ec"", ""bc1c7285-ac47-4d06-b4c6-6f3780cbfb3f"" } ","DISCOUNT","1f028ecb-4115-4378-b00e-3fcd4cbdf54d","2019-04-23T03:50:31.226","Angelo.Woods@test.npe.paradise.org","<div>Employees now have access to an exclusive member offers portal through the Samsung Employee Purchase program. Get up to 35% off the RRP across a wide range of products including mobile phones, tablets, televisions, refrigerators, washing machines, monitors and more.</div><div><br /></div><div><br /><br /></div>","dws-tile-images-ptest","samsung.jpg","2019-04-23T03:50:31.228","Angelo.Woods@test.npe.paradise.org","PUBLISHED","ce07c19b-dd87-4890-9ad3-6acfe0998496","Samsung","2019-04-17T01:00","2025-01-01T23:00","https://shop.samsung.com/au/multistore/auepp/austpost_au/","Simply go to link provided to access the site. Browse our categories, add your products to cart and checkout. Happy Shopping! "
```

- Generate a key:value hashtable used later. Key is "id" column, value is "image_key" colomn:

```console
𝜆 awk -F"\",\"" '{print $1 ":" $9 "\""}' dws-tile-publisher-test-tile.csv | sed 's/jpg/json/' | sed 's/png/json/' | sed -n '1d;p'
"950c529b-d6ae-472b-b44a-510ec201c167:Samsung-Galaxy-10-Leak.json"

...

"095cc9b4-fd64-4479-8817-0b35b8ddcbc2:samsung.json"
```

- Run the bash script and retrieve item from AWS DyanmoDB table, tranform it and output into a JSON file for import into AWS Production production table:

<script src="https://gist.github.com/TerrenceMiao/de10c70aa6adf212fae6e9fc4d63c025.js"></script>

- A generated JSON file looks like this:

```console
𝜆 cat Samsung-Galaxy-10-Leak.json
{
    "status": {
        "S": "PUBLISHED"
    },
    "valid_from": {
        "S": "2019-03-24T01:00"
    },
    "sub_category_id": {
        "S": "6936a6ed-fa97-4439-9129-c43e288011b3"
    },
    "description": {
        "S": "Testing descriptions"
    },
    "image_key": {
        "S": "Samsung-Galaxy-10-Leak.jpg"
    },
    "claim_url": {
        "S": "http://www.google.com"
    },
    "last_modified_date": {
        "S": "2019-04-23T01:02:18.125"
    },
    "valid_to": {
        "S": "2021-02-25T23:00"
    },
    "created_user": {
        "S": "Noel.French@paradise.org"
    },
    "benefit_type": {
        "S": "DISCOUNT"
    },
    "last_modified_user": {
        "S": "Noel.French@paradise.org"
    },
    "created_date": {
        "S": "2019-04-23T01:02:18.123"
    },
    "title": {
        "S": "Samsung Galaxy 10"
    },
    "category_id": {
        "S": "8d27fb2d-c5f8-487a-a869-2ffd0e476eb6"
    },
    "image_bucket": {
        "S": "dws-tile-images-prod"
    },
    "id": {
        "S": "950c529b-d6ae-472b-b44a-510ec201c167"
    },
    "available_to": {
        "SS": [
            "0b1b9ed4-e171-4eaf-9d20-1a870ab7cc7c",
            "2df9daf4-da50-4ff9-9322-969d02c178f4",
            "b6fdcdc7-ffe6-4007-ae56-f5ab545768ec",
            "bc1c7285-ac47-4d06-b4c6-6f3780cbfb3f"
        ]
    }
}
```

- Now run the bash script and import JSON file into AWS Production DynamoDB table:

<script src="https://gist.github.com/TerrenceMiao/c61f7721fe9721d7f6983cce821e7929.js"></script>
