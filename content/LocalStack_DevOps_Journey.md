+++
title = "Fake It Till You Make It: A LocalStack DevOps Journey"
date = "2025-02-16"
description = "Usages for mocking AWS Services locally."
[taxonomies]
tags = ["AWS", "LocalStack","DevOps","Cloud Development","Infrastructure as Code","Terraform","Golang", "Kubernetes","Testing","Mocking","Sandbox","CI/CD","Developer Productivity"]
+++

# Why Spin Up a Fake Local Cloud?

When working on cloud infrastructure, we're often curious about where our Infrastructure as Code (IaC) efforts will lead.
The end result is what truly counts, but during development, tools that focus on managing state and detecting drift often leave us with an incomplete picture—especially when the infrastructure wasn’t entirely created or updated by these tools.  

On top of that, building and applying cloud models often takes time. We're left in limbo, waiting for results and hoping we accounted for every detail.  
While these tools are invaluable for ensuring consistency and spotting configuration drift, they don't always provide a way to simulate how our infrastructure will behave in a live environment.  

This is where LocalStack comes in. As a cloud development framework for AWS, it offers an extra layer of clarity by enabling us to test our infrastructure locally.  
By doing so, we reduce wait times, improve confidence in our models, and even gain a chance to validate integration between services.  

This post dives into [LocalStack](https://www.localstack.cloud/), exploring how it bridges the gap between theory and reality in cloud infrastructure development.  

# Setting the Stage

Rather than rattling off a vague list of the tools and components in play, I’ll gladly share a setup you can follow along with at your own pace.  
We'll create our own sandbox to experiment in, and that means "your toys are just as good as mine." While I'll share the tools I’m using, feel free to substitute them with alternatives that work for you.

## LocalStack

Our end goal is to develop against a mocked version of AWS services and build tools around it. The first step is installing LocalStack.
There are several ways to do this according to the [documentation](https://docs.localstack.cloud/getting-started/installation/): you can use the dedicated CLI, run it as a container, or set it up with Docker Compose.

There are many types of software that communicate with an AWS cloud in the IaC landscape, often leveraging the strengths of Kuberenetes.
For that reason, let's run it in a local cluster, following up with installing Localstack through [Helm](https://docs.localstack.cloud/getting-started/installation/#helm).

Let's highlight a few commands!  
Adding the repo through:
```bash
helm repo add localstack-repo https://helm.localstack.cloud
```
then installing it via:
```bash
helm upgrade --install localstack localstack-repo/localstack -n localstack --create-namespace
```

## Dialing In: Connecting to our local cloud

Now that LocalStack is running, we have several ways to interact with it.

If you installed LocalStack using Helm in the previous section, you can expose it locally by running:
```bash
nohup kubectl -n localstack port-forward svc/localstack 4566:4566 &
```
`nohup` keeps the process running even when the terminal is closed  
`&` frees up our terminal again.

One approach is to use wrapper scripts like **awslocal** and **tflocal**, which serve as drop-in replacements for the **AWS CLI** and **Terraform**, respectively. 
While these can be convenient, I prefer using the actual CLI tools and configuring them to work seamlessly within a controlled environment.
A key advantage of this approach is flexibility—being able to define a directory as a self-contained sandbox where configurations automatically adjust when you step inside. 
To achieve this, I use [Shadowenv by Shopify](https://shopify.github.io/shadowenv/).

## Do You Want to Build a Sand Castle?

Let's get started by creating our sandbox directory. With Shadowenv installed, we first add the hidden directory it monitors:
```bash
mkdir .shadowenv.d
```
Inside that hidden directory, we'll create a Lisp file that executes whenever we enter the sandbox:
```bash
vim sandbox.lisp
```
To verify that Shadowenv is working, let's add a simple environment variable:
```lisp
(env/set "MSG" "Hello from the Sandbox!")   
```
We're now left with actually trusting the sandbox directory for Shadowenv execution, we do this executing the **shadowenv trust** command from inside the directory.  
Great, now outside our directory we won't get any result issuing the **echo $MSG** command, inside it we'll see our lovely greeting.

### Configuring AWS CLI for LocalStack

Next, let's configure the AWS CLI to interact with LocalStack. The available environment variables are listed in the [docs](https://docs.aws.amazon.com/cli/v1/userguide/cli-configure-envvars.html).
Since we've forwarded ports for LocalStack, we need to include that information in our AWS CLI configuration. The config file is located at `$HOME/.aws/config`.
We'll create a new profile called localstack, adding the following lines:
```
[profile localstack]
region = us-east-1
output = json
endpoint_url = http://localhost:4566
```
Great, now let's set that profile to be the default one on entring that sandbox directory we made, for this we add the following line in our `sandbox.lisp` file:
```lisp
(env/set "AWS_PROFILE" "localstack")
```

### Testing 1..2..

To verify our doings, from within our sandbox directory we can execute a simple command like `aws s3api list-buckets` and we'll get back a JSON object:
```json
{
    "Buckets": [],
    "Owner": {
        "DisplayName": "webfile",
        "ID": "75aa57f09aa0c8caeab4f8c24e99d10f8e7faeebf76c078efc7c6caea54ba06a"
    },
    "Prefix": null
}
```
With this setup, we now have a controlled sandbox environment where we can freely experiment using the **AWS CLI**. 

### Feeling the Sand

Using the **AWS CLI** was just a first step. When managing cloud infrastructure, it's best to have a **reusable blueprint**—and that's where **Infrastructure as Code (IaC)** comes in.  
For simplicity, we'll start with **Terraform**, though there are many other tools we could use to manage our fake cloud, often leveraging Kubernetes. I plan to explore those in a later post!
Before we start writing, let's take a look at what the [AWS Provider for Terraform](https://registry.terraform.io/providers/hashicorp/aws/latest/docs) requires.  
We can see that the region should be configured. For our next example we should also define an endpoint for **S3** in our setup.
To make **Terraform** aware of these settings, we'll update our sandbox environment variables. 
Instead of hardcoding values, we’ll use the `TF_VAR` prefix so Terraform can pick them up from environment variables dynamically.  
With these changes, our sandbox.lisp should look as follows:
```lisp
; General 
(env/set "LOCALSTACK_URL" "http://localhost:4566")
(env/set "LOCALSTACK_URL_S3" "http://s3.localhost.localstack.cloud:4566")
; AWS CLI
(env/set "AWS_PROFILE" "localstack")
; Terraform
(env/set "TF_VAR_region" "us-east-1")
(env/set "TF_VAR_profile" (env/get "AWS_PROFILE"))
(env/set "TF_VAR_url" (env/get "LOCALSTACK_URL"))
(env/set "TF_VAR_url_s3" (env/get "LOCALSTACK_URL_S3"))
```

### Buckets of Fun: Terraforming S3

Now let's create a Terraform configuration to define an S3 bucket with some test content. Create a file named main.tf in your sandbox directory:
```terraform
variable "region" {
  type = string
}
variable "url" {
  type = string
}
variable "url_s3" {
  type = string
}

provider "aws" {
  region = var.region

  s3_use_path_style           = false
  skip_credentials_validation = true
  skip_metadata_api_check     = true
  skip_requesting_account_id  = true

  endpoints {
    s3  = var.url_s3
  }
}

resource "aws_s3_bucket" "test_bucket" {
  bucket = "henry"
}

resource "aws_s3_object" "test_bucket_content" {
  bucket  = "henry"
  key     = "data"
  content = "aHR0cHM6Ly90ZW5vci5jb20vdmlldy9ub3NlLWNhbmR5LWdpZi0yNzAwMzYxMQo="

  depends_on = [aws_s3_bucket.test_bucket]
}
```
Here’s what’s happening:

  -  We define variables for the region and LocalStack endpoints.
  -  The AWS provider is set up to use LocalStack, skipping unnecessary credential checks.
  -  We create an S3 bucket named henry.
  -  Inside the bucket, we store an S3 object containing a small test payload.  
  
### Testing 3..4..  

After running **terraform init**, **- plan**, and **- apply**, we can verify that the object was created with `echo $(aws s3 cp s3://henry/data -)`.
Our terminal should print out the content we've stored in that data key.   

Awesome! We've now used an actual Infrastructure as Code tool to interact with our local cloud.

### Keeping Things in Context

Our **kubectl** commands should also only run against our minikube cluster when inside our sandbox directory.
To achieve that we'll store our minikube config in a dedicated file:
```bash
kubectl config view --minify > ~/.kube/minikube-config
```
And set our kubeconfig in our sandbox.lisp:
```lisp
(env/set "KUBECONFIG" "~/.kube/minikube-config")
```

### Burrowing Deeper: A Gopher in Our Sandbox

We're not constrained to IaC alone, many clouds offer an SDK, that means we're able to approach our LocalStack using a programming language like Go.
To simply demontrate us fetching our beloved bucket and it's content we can build and run the following program:
```go
package main

import (
	"context"
	"fmt"
	"github.com/aws/aws-sdk-go-v2/aws"
	"github.com/aws/aws-sdk-go-v2/config"
	"github.com/aws/aws-sdk-go-v2/service/s3"
	"io"
	"log"
	"os"
)

func main() {
	ctx := context.Background()
	cfg, err := config.LoadDefaultConfig(ctx)
	if err != nil {
		log.Fatal(err)
	}

	client := s3.NewFromConfig(cfg, func(o *s3.Options) {
		o.BaseEndpoint = aws.String(os.Getenv("LOCALSTACK_URL_S3"))
	})

	output, err := client.GetObject(ctx, &s3.GetObjectInput{
		Bucket: aws.String("henry"),
		Key:    aws.String("data"),
	})
	if err != nil {
		log.Fatal(err)
	}

	bytes, err := io.ReadAll(output.Body)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Print(string(bytes))
}
```

# Shaking Off the Sand

We've explored multiple ways to integrate with our fake cloud, from simple CLI commands to Infrastructure as Code and high-level programming with Go.
While this might seem like a collection of low-hanging fruit, we've seen that our sandbox is more than just a playground.  

The result is a lightweight, cost-effective environment that allows us to validate configurations and test the logic of our setup in a highly integrated way.  
This flexibility makes it a valuable tool not just for experimentation but also for CI pipelines—whether as a test container without orchestration or as a deployment within a local Kubernetes cluster.

Thanks for joining me in this madness—see you on our next venture!