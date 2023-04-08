---
layout: post
title: "Automating Scheduled Scaling Down of AWS ECS Services with Terraform to Optimize Cost Savings"
date: 2023-04-08 11:00:00 +0700
categories: [Terraform, AWS]
tags: [terraform, aws]
---

---
```terraform
resource "aws_appautoscaling_target" "ecs" {
 max_capacity       = 4
 min_capacity       = 1
 resource_id        = "service/clusterName/serviceName"
 scalable_dimension = "ecs:service:DesiredCount"
 service_namespace  = "ecs"
}

resource "aws_appautoscaling_scheduled_action" "ecs" {
 name               = "ecs"
 service_namespace  = aws_appautoscaling_target.ecs.service_namespace
 resource_id        = aws_appautoscaling_target.ecs.resource_id
 scalable_dimension = aws_appautoscaling_target.ecs.scalable_dimension
 schedule           = "cron(00 11 ? * MON-FRI *)"

 scalable_target_action {
   min_capacity = 0
   max_capacity = 0
 }
}
```