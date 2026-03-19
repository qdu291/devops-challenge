Provide your CLI command here:
jq -r 'select(.symbol == "TSLA" and .side == "sell") | .order_id' ./transaction-log.txt | xargs -P 4 -I{} curl -s "https://example.com/api/{}" > ./output.txt
