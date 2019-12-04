# What is Dask Distributed?

**Dask.distributed**: is a lightweight and open source library for distributed computing in Python. It is also a centrally managed, distributed, dynamic task scheduler. Dask has three main components:

**dask-scheduler process**: coordinates the actions of several workers. The scheduler is asynchronous and event-driven, simultaneously responding to requests for computation from multiple clients and tracking the progress of multiple workers.

**dask-worker processes**: Which are spread across multiple machines and the concurrent requests of several clients.
dask-client process: which is is the primary entry point for users of dask.distributed

# Step 1
First, create an Elastic Container Registry (ECR) Repository as a pre-requisite. To do this, I go to the AWS Console -> Elastic Container Service (ECS) -> Select Repositories then “Create repository”. We need to give it a name — “dask” then click “Next Step”:

Then, we move to the “Get Started” page where we find the initial commands to create and push the container image.

# Step 2
Now that the repository is ready, switch to a shell “Terminal” to download, build and tag the container image then push it to ECR. To do that, run the following shell commands:

```
bash# git clone https://github.com/wmlba/ECS-Dask.git
bash# cd ECS-Dask; tar -xzf base-image.tar.gz; cd base-image
bash# `aws ecr get-login --no-include-email --region us-east-1`
bash# docker build -t dask .
bash# docker tag dask:latest <AWS Account ID>.dkr.ecr.us-east-1.amazonaws.com/dask:latest
bash# docker push <AWS Account ID>.dkr.ecr.us-east-1.amazonaws.com/dask:latest
```

*NOTE: In order to run the above commands, you need to have Docker installed locally on the machine you run the commands from and the AWS credentials configured.*

# Step 3

Launch cloudformation template

The CloudFormation stack will create resources such as: Fargate Cluster, Task Definitions, Services and Tasks for both Dask worker and Scheduler. It will also create an IAM Execution Role and Policy to allow access to Elastic Container Registry (ECR) repository and CloudWatch log groups for logs. All the resources will be created in the us-east-1 region by default.

# Step 4

On the Specify Details page, I specify a private subnet with a NAT gateway for the cluster. The Dask Scheduler and Workers will communication over a private network and the NAT gateway is only needed for the ECS Service to be able to pull the ECR image from the repository. Then, select Next:

Review and create

# Step 5

From a sagemaker notebook in the same VPC, run

```
conda update -y dask
```

# Step 6
From the same notebook, check to see if you connect to the cluster

```
from dask.distributed import Client
client = Client('Dask-Scheduler.local-dask:8786')
client
```


### References

More details on: https://medium.com/@will.badr/serverless-distributed-data-pre-processing-using-dask-amazon-ecs-and-python-part-1-a6108c728cc4
