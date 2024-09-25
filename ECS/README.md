# Complete AWS ECS Setup Guide

This guide walks through setting up Amazon ECS on AWS, including setting up an ECS service and exposing your application using either a Load Balancer or a public IP.

---

## Prerequisites

Before starting, make sure you have the following:

- **AWS CLI**
- **Docker**
- **AWS account**

---

## Step 1: Create an IAM user and Connect to AWS Console

### 1 Install AWS CLI and Configure Locally

1. Install the AWS CLI on your local machine.
2. Create an AWS IAM role with needed permissions.
3. Run `aws configure` and enter your **AWS Access Key ID**, **Secret Access Key**, and **default region**.

---

## Step 2: Create an Amazon ECR Repository

1. Navigate to **ECR** in the AWS Console.
2. Create a new ECR repository to store your Docker images.
3. (See need comand son AWS) Use the AWS CLI to authenticate Docker with ECR:

   `[AWS_ECR_LOGIN_COMMAND]`

4. Build and push your Docker image:

   - Build the Docker image locally:
     `docker build -t my-app .`

   - Tag the Docker image:
     `docker tag my-app:latest <aws-account-id>.dkr.ecr.<region>.amazonaws.com/my-app-repo:latest`

   - Push the Docker image to ECR:
     `docker push <aws-account-id>.dkr.ecr.<region>.amazonaws.com/my-app-repo:latest`

---

## Step 3: Create an ECS Cluster

1. In the **ECS** section of the AWS Console, click **Create Cluster**.
2. Choose either **Networking only** (for Fargate) or **EC2 + Networking** depending on your launch type.
3. Configure your cluster (name, VPC, subnets, etc.).
4. Click **Create**.

---

## Step 4: Create a Task Definition

1. Go to **Task Definitions** in the ECS Console and click **Create new Task Definition**.
2. Choose **Fargate** or **EC2** depending on your launch type.
3. Configure the task:
   - **Task Definition Name**: (e.g., `my-task`)
   - **Task Role**: Select the task execution role created earlier.
   - **Task Size**: Choose the CPU and memory settings.
   - Enable **CloudWatch Logs** to capture task logs.
4. Under **Container Definitions**, add your container:

   - **Container Name**: (e.g., `my-app-container`)
   - **Image**: Enter the ECR image URI (e.g., `<aws-account-id>.dkr.ecr.<region>.amazonaws.com/my-app-repo:latest`).
   - **Port Mappings**: Map the container's port (e.g., `80`).

5. Click **Create**.

---

## Step 5: Create an ECS Service

1. Go to **ECS Services** in the AWS Console and click **Create Service**.
2. Select the **Fargate** or **EC2** launch type based on your setup.
3. Choose the task definition created in Step 5, and configure the following:

   - **Service Name**: (e.g., `my-app-service`)
   - **Number of Tasks**: Specify the number of desired tasks (e.g., `1` for a single instance, or more for scaling).

4. **Load Balancer** (Optional):

   - If you want to expose the service publicly, select **Application Load Balancer**.
   - Configure **Listener** rules, **Target Groups**, and **Health Checks**.
   - Make sure that the load balancer is in the correct subnets with internet access.

5. **Auto Scaling** (Optional):

   - Set up auto-scaling policies based on CPU, memory, or custom metrics.

6. Click **Create Service**.

---

## Step 6: Set Up Auto Scaling (Optional)

1. Navigate to **ECS Service Auto Scaling** and configure policies to automatically scale your service based on demand.
2. For **Fargate**, set scaling policies for task count.
3. For **EC2**, ensure EC2 instances are in an **Auto Scaling Group**.

---

## Step 7: Expose Your Service

Once your ECS service is running, you have two options to expose your application:

### Option 1: Use an Application Load Balancer (ALB)

1. If you configured an **Application Load Balancer (ALB)** during ECS Service creation:

   - The ALB will route traffic to the ECS tasks across multiple availability zones.
   - Make sure to set up proper **DNS** records (such as using Route 53) to point to the ALB.

   Benefits:

   - High availability and fault tolerance.
   - Built-in health checks to ensure only healthy tasks receive traffic.

### Option 2: Assign a Public IP to ECS Tasks

1. If you're running **Fargate** tasks, you can directly expose the tasks by assigning them a public IP.
2. When creating your ECS service, ensure **Auto-assign public IP** is enabled.
3. Access the application via the task's public IP address.

   Drawbacks:

   - This option doesn't provide high availability.
   - Each task will have a unique public IP, making DNS configuration more complex.

---

## Step 8: Logging and Monitoring

1. Ensure **CloudWatch Logs** are enabled in your Task Definition to monitor logs.
2. Set up **CloudWatch Alarms** based on key metrics (e.g., CPU utilization, memory usage).

---
