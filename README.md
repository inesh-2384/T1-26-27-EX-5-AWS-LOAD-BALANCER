# T1-26-27 EX 5 — AWS LOAD BALANCER
### NAME: INESH N
### REG NO: 212223220036
## AIM

To create an Application Load Balancer with an Auto Scaling Group, distribute traffic across multiple EC2 instances, and automatically scale the infrastructure based on CPU utilization.

## ALGORITHM

1. Create an AMI from the existing Web Server 1 instance.
2. Create a target group and Application Load Balancer.
3. Create a launch template using the AMI.
4. Create an Auto Scaling Group using the launch template.
5. Configure the Auto Scaling Group with minimum, desired, and maximum instances.
6. Configure a target tracking scaling policy using CPU utilization.
7. Verify that the Load Balancer distributes traffic to healthy instances.
8. Generate load and verify automatic scaling.
9. Terminate the original Web Server 1 instance.

## PROCEDURE

### Step 1: Create AMI

Open **EC2 → Instances** and select **Web Server 1**.

Wait until **2/2 Status Checks passed**.

Choose:

**Actions → Image and templates → Create image**

Configure:

| Setting | Value |
|---|---|
| Image Name | `WebServerAMI` |
| Description | `Lab AMI for Web Server` |

Choose **Create image**.

#### Output

<img width="1918" height="873" alt="image" src="https://github.com/user-attachments/assets/6a024b15-7a24-4c20-96cf-c0dd598692f2" />


---

### Step 2: Create Target Group

Open **EC2 → Target Groups → Create target group**.

Configure:

| Setting | Value |
|---|---|
| Target Type | Instances |
| Target Group Name | `LabGroup` |
| VPC | `Lab VPC` |

Skip target registration and choose **Create target group**.

#### Output

<img width="1919" height="864" alt="image" src="https://github.com/user-attachments/assets/aefa9e68-ac5b-4361-91cb-6017b3c974dd" />


---

### Step 3: Create Application Load Balancer

Open **EC2 → Load Balancers → Create load balancer**.

Select **Application Load Balancer**.

Configure:

| Setting | Value |
|---|---|
| Name | `LabELB` |
| Scheme | Internet-facing |
| VPC | `Lab VPC` |
| Subnet 1 | `Public Subnet 1` |
| Subnet 2 | `Public Subnet 2` |
| Security Group | `Web Security Group` |
| Listener | HTTP : 80 |
| Default Action | Forward to `LabGroup` |

Choose **Create load balancer**.

#### Output

<img width="1919" height="856" alt="image" src="https://github.com/user-attachments/assets/8e41cda4-26da-4627-97ba-202f549e821c" />


---

### Step 4: Create Launch Template

Open **EC2 → Launch Templates → Create launch template**.

Configure:

| Setting | Value |
|---|---|
| Launch Template Name | `LabConfig` |
| AMI | `WebServerAMI` |
| Instance Type | `t2.micro` |
| Key Pair | `vockey` |
| Security Group | `Web Security Group` |
| Detailed CloudWatch Monitoring | Enabled |

Choose **Create launch template**.

#### Output
<img width="1919" height="875" alt="image" src="https://github.com/user-attachments/assets/720d1d2b-5c30-4d22-ace4-adac96e9be6f" />


---

### Step 5: Create Auto Scaling Group

From the `LabConfig` launch template, choose **Actions → Create Auto Scaling group**.

Configure:

| Setting | Value |
|---|---|
| Auto Scaling Group Name | `Lab Auto Scaling Group` |
| Launch Template | `LabConfig` |
| VPC | `Lab VPC` |
| Subnets | `Private Subnet 1`, `Private Subnet 2` |
| Load Balancer Target Group | `LabGroup` |

Enable **group metrics collection within CloudWatch**.

#### Group Size

| Setting | Value |
|---|---:|
| Desired Capacity | 2 |
| Minimum Capacity | 2 |
| Maximum Capacity | 6 |

#### Scaling Policy

| Setting | Value |
|---|---|
| Policy Name | `LabScalingPolicy` |
| Policy Type | Target Tracking |
| Metric | Average CPU Utilization |
| Target Value | 60% |

Add the tag:

| Key | Value |
|---|---|
| Name | `Lab Instance` |

Choose **Create Auto Scaling group**.

#### Output

> Paste the screenshot showing the Auto Scaling Group with desired capacity 2.

---

### Step 6: Verify Load Balancing

Open **EC2 → Target Groups → LabGroup → Targets**.

Wait until both `Lab Instance` targets show:

```text
Status: healthy
Then open EC2 → Load Balancers → LabELB.
```
Copy the DNS Name and open it in a browser.

### Output

Paste the screenshot showing both healthy targets.

Paste the screenshot showing the application accessed through the Load Balancer DNS.

### Step 7: Test Auto Scaling

Open CloudWatch → All Alarms.

Verify the Auto Scaling alarms.

Return to the web application and choose:

Load Test

This generates CPU load on the instances.

Monitor the CloudWatch alarms until the high CPU alarm changes to:

In alarm

Auto Scaling should launch additional instances.

Open EC2 → Instances and verify that more than two Lab Instance instances are running.

### Output

Paste the screenshot showing the CloudWatch alarm in In alarm state.

Paste the screenshot showing additional Lab Instance EC2 instances.

### Step 8: Terminate Web Server 1

Select Web Server 1.

Choose:

Instance state → Terminate instance

Confirm the termination.

### Output

Paste the screenshot showing Web Server 1 terminated.

## OUTPUT

The Application Load Balancer successfully distributes traffic across healthy EC2 instances, while the Auto Scaling Group automatically launches additional instances when CPU utilization increases.
```
AMI                 : WebServerAMI
Target Group        : LabGroup
Load Balancer       : LabELB
Launch Template     : LabConfig
Auto Scaling Group  : Lab Auto Scaling Group
Desired Instances   : 2
Minimum Instances   : 2
Maximum Instances   : 6
Scaling Policy      : LabScalingPolicy
CPU Target          : 60%
```
## RESULT

Thus, an AWS Application Load Balancer and Auto Scaling infrastructure were successfully created and configured. Traffic was successfully distributed across healthy EC2 instances, and additional instances were automatically launched when CPU utilization increased.
