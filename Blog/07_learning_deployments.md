# Learning Deployments

This is gonna be the fun part. I want to get everything set up so I can deploy

* https://github.com/dawguy/kelly-criterion-client
* https://github.com/dawguy/Blog-Angular
* https://github.com/dawguy/blog-backend

I want to have 3 deployed behind a single reverse proxy and point at the correct targets.

Ideally this would also be a good time to learn kubernetes and terraform since that's been something on my to do list for a while.

## Terraform & Kubernetes

Seems like step 1 is going to be to get more familiar with kubernetes & dockerize my apps.

In particular Terraform seems like its going to be the last one I learn as it seems like its more of a declarative language for using the other services.

One thing I am going to try to do for now will be to host the `minikube` on a spare desktop computer rather than play around with 

## Starting here
https://kubernetes.io/docs/tutorials/

## Scratch That
From what I've been looking at, I feel intimidated by kubernetes. Going to start with docker for hosting + anything else needed to get that visible from my local desktop as the host. Goal is to learn the foundations before moving onto more advanced stuff.

Also looked a bit at AWS.
* Lambda
* Fargate
* EC2
* EKS (Amazon's kubernetes)

For me I have some interest in lambda. Would be really cool to have an S3 bucket which receives a draft post, then automatically processes it and turns it into a .html page which can be served statically.

But overall these all seem like stuff that I'd rather do locally first to get a better understanding then move on.

## Self Hosted Wiki

https://github.com/r-selfhosted-wiki/wiki.git


