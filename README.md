# devops-helpers


## AWS
- Accessing AWS Elasticsearch - https://www.jeremydaly.com/access-aws-vpc-based-elasticsearch-cluster-locally/

- Before using AWS CLI:
  - set region ``export AWS_REGION=us.east.1``
  - set profile ``export AWS_PROFILE=sts``


- Move and rename all S3 objects with the same prefix path:
`` #!/bin/bash

bucketname='bluebite-enablement-logging'
for key in $(aws s3api list-objects --bucket "${bucketname}" --prefix "bluebite-enablement" --query "Contents[].{Object:Key}" --output text) ;
  do
    echo "$key"
    FILENAME=$($key | grep -o '.\{36\}$')
    echo $FILENAME
    #aws s3 mv s3://$bucketname/$key s3://$bucketname/access/$FILENAME
  done ``

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


- Generate SSH Keypair:
``ssh-keygen -P "" -t rsa -b 4096 -m pem -f ~/.ssh/my-key-pair``
   - -P: is for passphrase. Intentionally set to empty.
   - -t: Specifies the type of key to create.  AWS EC2 Key Pair requires RSA. It's my experience that this pertains to the public key that is created.
   - -b: Specifies the number of bits in the key. The supported lengths are 1024, 2048, and 4096. If you connect using SSH while using the EC2 Instance Connect API, the supported lengths are 2048 and 4096.
   - -m: Specifies a key format for key generation. Setting a format of “PEM” when generating a supported private key type will cause the key to be stored in the legacy PEM private key format.  AWS EC2 Key Pair need the legacy format
   - -f: Specifies the output filename and path of the key file
   
