---
layout: post
title: "Scheduled Scaling Down of AWS ECS Services with Terraform to Optimize Cost Savings"
date: 2023-04-08 11:00:00 +0700
categories: [Terraform, AWS]
tags: [terraform, aws]
---

---
### Introduction
To save costs, we can scale down the ECS services during off-hours and scale them up when needed[^1], particularly in development and testing environments.
### Terraform code snippet
```terraform
resource "aws_appautoscaling_target" "ecs" {
 max_capacity       = 4
 min_capacity       = 2
 resource_id        = "service/clusterName/serviceName"
 scalable_dimension = "ecs:service:DesiredCount"
 service_namespace  = "ecs"
}

resource "aws_appautoscaling_scheduled_action" "scale_down" {
 name               = "ecs_scale_down"
 service_namespace  = aws_appautoscaling_target.ecs.service_namespace
 resource_id        = aws_appautoscaling_target.ecs.resource_id
 scalable_dimension = aws_appautoscaling_target.ecs.scalable_dimension
 schedule           = "cron(00 18 ? * MON-FRI *)"

 scalable_target_action {
   min_capacity = 0
   max_capacity = 0
 }
}

resource "aws_appautoscaling_scheduled_action" "scale_up" {
 name               = "ecs_scale_up"
 service_namespace  = aws_appautoscaling_target.ecs.service_namespace
 resource_id        = aws_appautoscaling_target.ecs.resource_id
 scalable_dimension = aws_appautoscaling_target.ecs.scalable_dimension
 schedule           = "cron(00 07 ? * MON-FRI *)"

 scalable_target_action {
   min_capacity = 2
   max_capacity = 4
 }
}
```
We can specify the time zone used when setting `aws_appautoscaling_scheduled_action`. If a time zone is not provided, UTC is used by default[^2].

### References
[^1]: [https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/appautoscaling_scheduled_action](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/appautoscaling_scheduled_action)
[^2]: [https://docs.aws.amazon.com/autoscaling/application/APIReference/API_PutScheduledAction.html#autoscaling-PutScheduledAction-request-Timezone](https://docs.aws.amazon.com/autoscaling/application/APIReference/API_PutScheduledAction.html#autoscaling-PutScheduledAction-request-Timezone)
