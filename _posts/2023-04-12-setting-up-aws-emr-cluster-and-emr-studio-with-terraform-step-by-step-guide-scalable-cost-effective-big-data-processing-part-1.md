---
layout: post
title: "Setting up AWS EMR Cluster and EMR Studio with Terraform: A Step-by-Step Guide for Scalable and Cost-Effective Big Data Processing - Part 1"
date: 2023-04-12 10:00:00 +0700
categories: [Terraform, AWS]
tags: [terraform, aws]
---

---
## Introduction
Lately, I was tasked to support a Data Scientist in setting up a Big Data Processing environment with Spark and Jupyter Notebooks. 
Unfortunately, I have not ever worked with or set up a Big Data environment before. 
The Data Scientist said that he was on a deadline, so he asked me to expedite this for him. 
The cost was also a concern, and he mentioned AWS EMR Serverless as a potential solution. 
AWS EMR Serverless provides a serverless runtime environment that simplifies the operation of analytics applications that use the latest open-source frameworks, such as Apache Spark and Apache Hive[^1]. 
Additionally, the Data Scientist expressed interest in using EMR Studio (Jupyter notebooks) for an interactive and collaborative environment to develop and execute Spark-based data processing tasks in a notebook format.

I understand that the Data Scientist was in a rush, and based on his suggestion about using EMR Serverless and EMR Studio, I decided to expedite the process by just referring to the documentation for `aws_emrserverless_application`[^2] and `aws_emr_studio`[^3] resource types in the AWS provider. 
After a few hours, I was able to successfully set up the EMR Serverless Application and EMR Studio with Terraform. 
However, I later discovered that EMR Studio is currently not supported with EMR Serverless.

And I had to set up an EMR Cluster (on EC2) instead. 
Unlike EMR Serverless, setting up an EMR Cluster requires careful consideration of factors such as scalability, costs, and proper setup. 
This time, I don't want to rush and make mistakes like before. I want to proceed steadily, not hastily. 
The first thing I need to do is thoroughly read the documentation about EMR Cluster and EMR Studio:
- [https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-what-is-emr.html](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-what-is-emr.html)
- [https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/emr_cluster](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/emr_cluster)
- [https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/emr_studio](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/emr_studio)

In this guide, I will demonstrate how to create a scalable and cost-effective big data processing environment in AWS using EMR Cluster and EMR Studio, leveraging the power of Terraform for infrastructure provisioning. 
This is Part 1, which focuses on setting up EMR Cluster. In Part 2, I will cover EMR Studio.

## Terraform code snippet
### Resource Tagging and Resource Name Prefix
`variables.tf`
```terraform
variable "resource_tags" {
  type = map(string)
}
```

`*.auto.tfvars.tf`
```
resource_tags = {
  Project     = "ProjectName",
  Environment = "Staging",
  Creator     = "TinhHN",
  CostCenter  = "Data",
  Status      = "Active",
  Managed-by  = "Terraform"
}
```

`main.tf`
```terraform
locals {
  prefix = "${var.resource_tags["Project"]}-${var.resource_tags["Environment"]}"
}
```

## References
[^1]: [https://docs.aws.amazon.com/emr/latest/EMR-Serverless-UserGuide/emr-serverless.html](https://docs.aws.amazon.com/emr/latest/EMR-Serverless-UserGuide/emr-serverless.html)
[^2]: [https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/emrserverless_application](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/emrserverless_application)
[^3]: [https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/emr_studio](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/emr_studio)
