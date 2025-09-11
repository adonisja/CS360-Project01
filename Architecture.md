## Updated Architecture Diagram (Text Style)

```
[React SPA (Browser)]  <--HTTPS--> [CloudFront] <- S3 (static build) / Amplify Hosting
     |                             |
     |--Auth: Cognito Hosted UI or Amplify Auth (JWTs) ----|
     |                                                   |
     |--- API calls (GraphQL AppSync or API Gateway REST) -> [Lambda resolvers / AppSync resolvers]
                                                           |
                +------------------------------------------+-----------------------------------+
                |                                          |                                   |
           [Aurora PostgreSQL (Private Subnet)]       [DynamoDB (if NoSQL chosen)]           [QLDB (optional)]
           (PHI / clinical data, relational rules)     (fast lookup, caches, queues)         (tamper-proof audit ledger)
                |                                          |                                   |
           [EFS / S3 for PDFs] <--- PDF generator Lambda ---|                                   |
                |                                          |                                   |
           [KMS CMK] (encrypt DB backups, S3 objects)      |                                   |
                                                           |
             Event orchestration: EventBridge / Step Functions -> Lambda workers -> SQS -> SNS/Pinpoint/Twilio/SES
                                                           |
             Payments: Stripe Connect (no card data stored; Stripe Checkout/Elements)            
                                                           |
             Observability & Compliance: CloudTrail, CloudWatch Logs, X-Ray, GuardDuty, Macie
```
