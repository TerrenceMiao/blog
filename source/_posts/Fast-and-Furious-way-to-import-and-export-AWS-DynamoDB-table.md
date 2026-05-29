---
title: Fast and Furious way to import and export AWS DynamoDB table
date: 2026-05-29 18:16:34
tags:
---

Extract data from the production tables and prepare JSON files for the test environment

```
for table in dashboard-tile-publisher-prod-group dashboard-tile-publisher-prod-tile dashboard-tile-publisher-prod-tile-event; do
  aws --profile production dynamodb scan \
    --table-name $table \
    --output json \
    --max-items 10000 \
  | jq --arg t "$table" '{ ($t): [ .Items[] | { "PutRequest": { "Item": . } } ] }' \
  > $table.json
done
```

Copy and modify the JSON files for the test environment
```
for suffix in group tile tile-event; do
  cp "dashboard-tile-publisher-prod-${suffix}.json" "dashboard-tile-publisher-ptest-${suffix}.json"
  sed -i '' "s/dashboard-tile-publisher-prod-${suffix}/dashboard-tile-publisher-ptest-${suffix}/g" "dashboard-tile-publisher-ptest-${suffix}.json"
done
```

Update the content in the JSON files for the test environment

```
for suffix in tile tile-event; do
  sed -i '' 's/dashboard-tile-publisher-images-prod/dashboard-tile-publisher-images-ptest/g' "dashboard-tile-publisher-ptest-${suffix}.json"
done
```

Import the modified JSON files into the test environment DynamoDB tables

```
for table in dashboard-tile-publisher-ptest-group dashboard-tile-publisher-ptest-tile dashboard-tile-publisher-ptest-tile-event; do
  jq -c --arg t "$table" '.[$t] | _nwise(25) | {($t): .}' "${table}.json" \
    | while IFS='' read -r chunk; do
        aws --profile test dynamodb batch-write-item \
          --no-cli-pager \
          --request-items "$chunk"; \
        sleep 10; # Add a small delay to avoid throttling  
      done
done
```

References
---

- A.I.