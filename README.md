# devops-helpers


## AWS

- Clear DynamoDB table:
``aws dynamodb scan --profile sts --region us-east-1 \
   --table-name trident_stage_event \
   --attributes-to-get "token" | \
   jq -r ".Items[] | tojson" | \
   tr '\n' '\0' | \
   xargs -0 -I keyItem \
    aws dynamodb delete-item --profile sts --region us-east-1 \
      --table-name trident_stage_event \
      --key=keyItem  ``
