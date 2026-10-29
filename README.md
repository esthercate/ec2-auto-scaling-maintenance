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

## Experiment 1: Rebooting an EC2 Instance

I rebooted an EC2 instance and checked its status after it came back online. The instance returned to service, and Auto Scaling did not replace it during this test.

## Experiment 2: Load Balancer Health Checks and Instance Replacement

I stopped Apache on one instance to simulate an application failure. The load balancer marked the target unhealthy, and Auto Scaling replaced the instance.

![Load balancer detects an unhealthy target](screenshots/17-alb-detects-unhealthy-target.png)

*The load balancer marked the target unhealthy after the web server stopped responding.*

![Auto Scaling replacement activity](screenshots/18-asg-replacement-activity.png)

*Auto Scaling recorded the replacement activity after the instance was marked unhealthy.*

During the test, I also encountered an issue with the launch template's user data script. I corrected the script so replacement instances could start the web server successfully.

## Solution 1: Put the Instance in Standby Before Maintenance

I moved an instance into Standby and chose not to replace it. I reduced the Auto Scaling group's minimum capacity for the test so the group could operate with one instance in service while the other was in Standby.

![Instance in Standby during maintenance](screenshots/21-asg-standby-maintenance.png)

*The instance was placed in Standby for controlled maintenance.*

After checking the instance, I returned it to service and checked the target group.

![Targets healthy after maintenance](screenshots/26-targets-healthy-after-maintenance.png)

*Both targets were healthy after the instance returned to service.*

The package check found no operating system updates available, so this test demonstrated the maintenance workflow rather than a completed OS patch.

## Solution 2: Suspend Unhealthy Instance Replacement During Maintenance

I suspended the `ReplaceUnhealthy` process and stopped Apache to simulate an application failure. The load balancer marked the target unhealthy, but Auto Scaling did not immediately replace the instance while replacement was suspended.

![ReplaceUnhealthy process suspended](screenshots/28-asg-replaceunhealthy-suspended.png)

*The replacement process was suspended before the failure test.*

After restoring Apache, the load balancer showed the application as healthy, but Auto Scaling still reported the instance as unhealthy.

![Instance health status before manual reset](screenshots/32-asg-instance-unhealthy-before-manual-reset.png)

*The target was healthy, but Auto Scaling still reported an unhealthy status.*

For this controlled experiment, I corrected the health status recorded by Auto Scaling, then resumed the health-check process followed by `ReplaceUnhealthy`. I verified the status of the instances and the load balancer targets afterwards.

![Both instances healthy after recovery](screenshots/33-asg-both-instances-healthy-after-manual-reset.png)

*Both instances were healthy after the recovery procedure.*

Suspending replacement can provide time to investigate a failure, but it does not stop health checks or fix the underlying problem. Manually setting an instance's status to healthy is not a substitute for resolving the cause of a failure.

## Key Lessons

- A reboot does not automatically mean an instance will be replaced.
- Load balancer health checks can help Auto Scaling detect application-level failures.
- A working launch template is important for successful instance replacement.
- Standby can provide a controlled way to take an instance out of service for maintenance.
- Suspending unhealthy instance replacement does not stop health checks or automatically correct an unhealthy status.
- Resource efficiency matters alongside application availability.

## Important Note

This was a controlled lab experiment. No operating system updates were available during the package check, so the project focuses on maintenance procedures, health checks, and instance replacement rather than demonstrating a completed OS patch.

## Region

`us-east-1`

## Author

- [LinkedIn](https://www.linkedin.com/in/catherine-esther-vuthi/)
- [X](https://x.com/Cate_ev)