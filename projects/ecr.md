Creating a Terraform module for an AWS ECR (Elastic Container Registry) repository involves defining the necessary resources, variables, and outputs. Here's a step-by-step guide:

1. **Define the module structure:**

   ```
   terraform-aws-ecr/
   ├── main.tf
   ├── variables.tf
   ├── outputs.tf
   ├── README.md
   ├── versions.tf
   └── examples/
       └── example-ecr-repo/
           ├── main.tf
           └── terraform.tfvars
   ```

2. **Define the `main.tf` file:**

   ```hcl
   // main.tf
   provider "aws" {
     region = var.aws_region
   }

   resource "aws_ecr_repository" "this" {
     name                 = var.repository_name
     image_tag_mutability = var.image_tag_mutability
     image_scanning_configuration {
       scan_on_push = var.image_scan_on_push
     }

     encryption_configuration {
       encryption_type = var.encryption_type
       kms_key         = var.kms_key
     }
     
     tags = var.tags
   }
   ```

3. **Define the `variables.tf` file:**

   ```hcl
   // variables.tf
   variable "aws_region" {
     description = "The AWS region to create the ECR repository in"
     type        = string
   }

   variable "repository_name" {
     description = "The name of the ECR repository"
     type        = string
   }

   variable "image_tag_mutability" {
     description = "The tag mutability setting for the repository. Can be one of MUTABLE or IMMUTABLE"
     type        = string
     default     = "MUTABLE"
   }

   variable "image_scan_on_push" {
     description = "Indicates whether images are scanned after being pushed to the repository (true) or not (false)"
     type        = bool
     default     = false
   }

   variable "encryption_type" {
     description = "The encryption type to use for the repository. Valid values are AES256 or KMS"
     type        = string
     default     = "AES256"
   }

   variable "kms_key" {
     description = "If using KMS encryption, specify the ARN of the KMS key"
     type        = string
     default     = null
   }

   variable "tags" {
     description = "A map of tags to assign to the resource"
     type        = map(string)
     default     = {}
   }
   ```

4. **Define the `outputs.tf` file:**

   ```hcl
   // outputs.tf
   output "repository_url" {
     description = "The URL of the created repository"
     value       = aws_ecr_repository.this.repository_url
   }

   output "repository_arn" {
     description = "The ARN of the created repository"
     value       = aws_ecr_repository.this.arn
   }

   output "repository_name" {
     description = "The name of the created repository"
     value       = aws_ecr_repository.this.name
   }
   ```

5. **Define the `versions.tf` file:**

   ```hcl
   // versions.tf
   terraform {
     required_version = ">= 0.12"

     required_providers {
       aws = {
         source  = "hashicorp/aws"
         version = "~> 3.0"
       }
     }
   }
   ```

6. **Create an example usage:**

   ```hcl
   // examples/example-ecr-repo/main.tf
   module "ecr" {
     source = "../.."

     aws_region            = var.aws_region
     repository_name       = var.repository_name
     image_tag_mutability  = var.image_tag_mutability
     image_scan_on_push    = var.image_scan_on_push
     encryption_type       = var.encryption_type
     kms_key               = var.kms_key
     tags                  = var.tags
   }

   output "repository_url" {
     value = module.ecr.repository_url
   }

   output "repository_arn" {
     value = module.ecr.repository_arn
   }

   output "repository_name" {
     value = module.ecr.repository_name
   }
   ```

7. **Define the `terraform.tfvars` file:**

   ```hcl
   // examples/example-ecr-repo/terraform.tfvars
   aws_region           = "us-west-2"
   repository_name      = "my-ecr-repo"
   image_tag_mutability = "MUTABLE"
   image_scan_on_push   = true
   encryption_type      = "KMS"
   kms_key              = "arn:aws:kms:us-west-2:123456789012:key/abcd1234-a123-456a-a12b-a123b4cd56ef"
   tags = {
     Environment = "dev"
     Project     = "my-project"
   }
   ```

8. **Provide a `README.md`:**

   ```markdown
   # terraform-aws-ecr

   This Terraform module creates an AWS ECR repository.

   ## Usage

   ```hcl
   module "ecr" {
     source = "path_to_your_module"

     aws_region            = var.aws_region
     repository_name       = var.repository_name
     image_tag_mutability  = var.image_tag_mutability
     image_scan_on_push    = var.image_scan_on_push
     encryption_type       = var.encryption_type
     kms_key               = var.kms_key
     tags                  = var.tags
   }
   ```

   ## Inputs

   | Name                 | Description                                                                 | Type        | Default  | Required |
   |----------------------|-----------------------------------------------------------------------------|-------------|----------|----------|
   | aws_region           | The AWS region to create the ECR repository in                              | string      | n/a      | yes      |
   | repository_name      | The name of the ECR repository                                              | string      | n/a      | yes      |
   | image_tag_mutability | The tag mutability setting for the repository (MUTABLE or IMMUTABLE)        | string      | "MUTABLE"| no       |
   | image_scan_on_push   | Indicates whether images are scanned after being pushed to the repository   | bool        | false    | no       |
   | encryption_type      | The encryption type to use for the repository (AES256 or KMS)               | string      | "AES256" | no       |
   | kms_key              | If using KMS encryption, specify the ARN of the KMS key                     | string      | null     | no       |
   | tags                 | A map of tags to assign to the resource                                      | map(string) | {}       | no       |

   ## Outputs

   | Name             | Description                              |
   |------------------|------------------------------------------|
   | repository_url   | The URL of the created repository        |
   | repository_arn   | The ARN of the created repository        |
   | repository_name  | The name of the created repository       |
   ```

This setup defines a reusable Terraform module for creating an AWS ECR repository. Users can customize the repository by providing values in the `terraform.tfvars` file, and the outputs provide useful information about the created repository.
