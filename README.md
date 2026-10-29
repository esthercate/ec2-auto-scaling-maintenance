# EC2 Auto Scaling Maintenance

## Overview

This project explores what happens when an EC2 instance managed by an AWS Auto Scaling group needs maintenance.

I tested how Auto Scaling responds when an instance is rebooted, when its web server becomes unhealthy, and when replacement is temporarily suspended during maintenance.

The goal was to understand how to maintain application availability while avoiding unnecessary instance replacement and resource usage.

## AWS Services Used

- Amazon EC2
- EC2 Auto Scaling
- Application Load Balancer (ALB)
- Elastic Load Balancing target groups
- AWS Systems Manager Session Manager
- AWS Identity and Access Management (IAM)

## What I Tested

1. Rebooting an EC2 instance and checking whether Auto Scaling replaced it.
2. Stopping the web server to see how load balancer health checks affected instance replacement.
3. Placing an instance in Standby before maintenance.
4. Suspending unhealthy instance replacement to investigate a simulated application failure.
5. Restoring the application and checking the health status before resuming normal Auto Scaling behaviour.

## Key Lessons

- A reboot does not automatically mean an instance will be replaced.
- Load balancer health checks can help Auto Scaling detect application-level failures.
- Standby can provide a controlled way to take an instance out of service for maintenance.
- Suspending unhealthy instance replacement does not stop health checks or automatically fix an unhealthy status.
- Checking health status and restoring normal Auto Scaling behaviour are important parts of maintenance.
- Resource efficiency matters alongside application availability.

## Important Note

This was a controlled lab experiment. No operating system updates were available during the package check, so the project focuses on maintenance procedures, health checks, and instance replacement rather than demonstrating a completed OS patch.

## Region

`us-east-1`

## Author

Documenting hands-on learning with AWS infrastructure and EC2 maintenance.
