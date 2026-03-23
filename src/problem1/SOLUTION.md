Provide your CLI command here:
`sudo apt-get install -y jq curl && jq -r 'select(.symbol == "TSLA" and .side == "sell") | .order_id' ./transaction-log.txt | xargs -P 4 -I{} curl -s "https://example.com/api/{}" > ./output.txt`


Output:
```
https://example.com/api/12346
https://example.com/api/12362
```