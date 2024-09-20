#Palo Alto Cortex XDR API details
CORTEX_XDR_API_ENDPOINT="https://api-(Enter_YOUR_Client_FQDN_URL).xdr.in.paloaltonetworks.com/public_api/v1/audits/management_logs"
CORTEX_XDR_API_KEY_ID="(Enter_YOUR_API_KEY_ID)"
CORTEX_XDR_API_KEY="(Enter_YOUR_API_KEY)"

# File to store the logs
OUTPUT_FILE="cortex_xdr_logs.json"
ERROR_LOG_FILE="cortex_xdr_error.log"

# Headers for Cortex XDR API
CORTEX_XDR_HEADERS=(
    "-H" "Accept: application/json"
    "-H" "Content-Type: application/json"
    "-H" "x-xdr-auth-id: $CORTEX_XDR_API_KEY_ID"
    "-H" "Authorization: $CORTEX_XDR_API_KEY"
)

# Payload for the Cortex XDR API request
PAYLOAD='{
  "request_data": {
    "search_from": 0,
    "search_to": 100,
    "sort": {
      "field": "timestamp",
      "keyword": "asc"
    }
  }
}'

# Function to get logs from Cortex XDR and save to a file
get_cortex_xdr_logs() {
    response=$(curl -s -w "%{http_code}" -o "$OUTPUT_FILE" -X POST "${CORTEX_XDR_HEADERS[@]}" -d "$PAYLOAD" "$CORTEX_XDR_API_ENDPOINT")
    if [ "$response" -eq 200 ]; then
        echo "Logs successfully retrieved from Cortex XDR and saved to $OUTPUT_FILE."
    else
        echo "Error retrieving logs from Cortex XDR. HTTP status code: $response" | tee "$ERROR_LOG_FILE"
        echo "Response output:" | tee -a "$ERROR_LOG_FILE"
        cat "$OUTPUT_FILE" | tee -a "$ERROR_LOG_FILE"
    fi
}

# Run the function to get logs and output to a JSON file
get_cortex_xdr_logs