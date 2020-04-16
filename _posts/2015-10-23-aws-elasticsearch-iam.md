---
layout: post
title: Using AWS Elasticsearch Service Without VPC
date:  2015-10-23 22:00:00 -0700
categories: posts
---

# {{ page.title }}

<small style="font-weight: 175;">{{ page.date | date: "%-d %B, %Y"}}</small>

Amazon's managed Elasticsearch service [recently launched](https://aws.amazon.com/about-aws/whats-new/2015/10/introducing-amazon-elasticsearch-service/) with a notable omission -- [no VPC support](https://forums.aws.amazon.com/thread.jspa?threadID=217059&tstart=0).

Having a seamlessly managed Elasticsearch cluster still proved quite appealing even without the built in VPC support. As a holdover until VPC support is available, I recently [pushed a package to pypi](https://pypi.python.org/pypi/aws-requests-auth) for communication with an AWS Elasticsearch cluster via AWS IAM and the python requests library.  It's possible to inject the aforementioned tool directly into the [elasticsearch-py](https://elasticsearch-py.readthedocs.org/en/master/) native client as well.

Check out the [source](https://github.com/DavidMuller/aws-requests-auth) or just get going with `pip install aws-requests-auth`.

Happy searching.
