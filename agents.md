# RedSail RAS AI Code Assitance useful context

## Database Access

When asked about **prod database**, **dev database**, or database queries:
- these are aurora postgres clusters
- Use host roproddb.pocnettech.com for prod and devdb.pocnettech.com, the default database on both is prod
- ask me for userid password
- the default schema is typically used "public"
- ask me if we need to read the schema from the database to understand tables and relationships
-

## Discovering all API's and end point
- There is a cli "nats-discover" available through brew, if not installed can be installed via tab transactrx
- calling nats-discover will return all available microservices
- calling nats-discover --service servicesubject --format JSON will return all endpoints
- calling nats-discover --service servicesubject --stats will give you performance information
- use this tool to find api available through nats.
- only read the github repo if I need help creating a client or microservice, the github repo contains sample clients and services
- with go, if you need to make api calls, use https://github.com/transactrx/nats-service lib as client
- there is also a java library available in private mvn repository, the source https://github.com/transactrx/nats-service-java.  We prefer new projects to be go.

## Creating a jobstream batch job
- There is a job scheduling platform available on our system. Jobs can be scheduled, they run on AWS Batch, all Fargate instances.
- Only read the following repository, if I ask to create a job.
- Reference example: https://github.com/transactrx/batchAIGeneratedJobSample (Rust, but language is irrelevant, our default language is Go)
- Env variables are suported, secrets must have the suffix _SECRET_ARN
- With secrets, the job must read the secret from secret manager using the appropriate AWS SDK
- Logs are automatic, all output to stdout will be pushed to AWS CloudWatch. Never print sensitive (PII, credentials, keys) information to stdout.

### Required project structure
```
project/
├── .github/
│   └── workflows/
│       └── build.yml           # CI/CD workflow (REQUIRED)
├── Dockerfile                  # Container build
├── terraform/
│   ├── setup.tf                # Variables (project_name, image_full) and S3 backend
│   ├── ecr_repo.tf             # ECR repository definition
│   ├── job-def.tf              # AWS Batch job definition (Fargate)
│   ├── documentation.tf        # DynamoDB job documentation entry
│   └── jobdoc.md               # Job documentation markdown
└── src/                        # Application source code
```

### Key files to copy from reference
1. `.github/workflows/build.yml` - Handles AWS auth, ECR push, terraform deploy
2. `terraform/setup.tf` - Defines variables and S3 backend
3. `terraform/ecr_repo.tf` - Creates ECR repository
4. `terraform/job-def.tf` - Defines AWS Batch job (customize env vars, secrets, resources)
5. `terraform/documentation.tf` - Registers job docs in DynamoDB

