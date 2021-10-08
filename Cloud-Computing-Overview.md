# 云计算基本概念

[TOC]



## 1 容器云平台概述

SaaS, PaaS, and IaaS are simply three ways to describe how you can use the cloud for your business.

- **IaaS:** cloud-based services, pay-as-you-go for services such as storage, networking, and virtualization.
  - 云服务的最底层，主要提供一些基础资源
- **PaaS:** hardware and software tools available over the internet.
  - 提供软件部署平台（runtime），抽象掉了硬件和操作系统的细节，可以无缝地扩展，开发者只要关注自己的业务细节，不需要关注底层。
- **SaaS:** software that’s available via a third-party over the internet.
  - 软件的开发，管理，部署都交给第三方，不需要关心技术问题，拿来即用。
- **On-premise**: software that’s installed in the same building as your business.

Here’s a great visual breakdown from [Hosting Advice](https://www.hostingadvice.com/how-to/iaas-vs-paas-vs-saas/):

<img src="./images/saas-vs-paas-vs-iaas-breakdown.jpeg" alt="docker-containerized-appliction"  />

## 2 Examples of SaaS, PaaS, and IaaS

Most businesses use a combination of SaaS and IaaS cloud computing service models, and many engage developers to create applications using PaaS, too.

**SaaS examples:** BigCommerce, Google Apps, Salesforce, Dropbox, MailChimp, ZenDesk, DocuSign, Slack, Hubspot.

**PaaS examples:** AWS Elastic Beanstalk, Heroku, Windows Azure (mostly used as PaaS), Force.com, OpenShift, Apache Stratos, Magento Commerce Cloud.

**IaaS examples:** AWS EC2, Rackspace, Google Compute Engine (GCE), Digital Ocean, Magento 1 Enterprise Edition*.

## 3 PaaS平台介绍

- 获得PaaS能力的几个必要条件：
  - 统一应用的运行时环境（Docker）
  - 有IaaS能力（K8S）
  - 有可靠的中间件集群、数据库集群（DBA的主要工作）
  - 有分布式存储集群（存储工程师的主要工作）
    - K8S其实有存储编排能力（PV/PVC）
      - PV：Persistent Volume
      - PVC：Persistent Volume Client
    - 为了简单，K8S多数采用NFS进行文件存储（）
  - 有适配的监控、日志系统（Prometheus、ELK）
  - 有完善的CI、CD系统（Jenkins、Spinnaker）
- 常见的基于K8S的CD系统
  - 自研
  - Argo CD
  - OpenShift（红帽发布的一个PaaS平台）
  - Spinnaker

> K8S具有存储编排能力、网络编排能力、运算资源编排能力、容器生命周期编排能力、就绪性探针（Readiness Probe）、存活性探针（Liveness Probe）

## 4 Spinnaker 简介

Spinnaker是Netflix在2015年开源的一款持续交付平台，继承了Netflix上一代集群和部署管理的工具Asgard：提高了持续交付系统的可复用性，提供了稳定可靠的API，提供了对基础设施和程序全局性的视图，配置、管理、运维都更简单。

### 4.1 主要功能

#### 集群管理

主要是管理云资源，主要是管理IaaS资源，比如OpenStack，Google云，微软云等。

#### 部署管理

管理部署流程是Spinnaker的核心功能，负责将Jenkins流水线创建的镜像，部署到Kubernetes集群中去，让服务真正运行起来。

### 4.2 架构

https://spinnaker.io/docs/reference/architecture/microservices-overview/

<img src="./images/Spinnaker Architecture Overview.png" alt="docker-containerized-appliction"  />

> ## Spinnaker microservices
>
> Spinnaker is composed of a number of independent microservices:
>
> - [Deck](https://github.com/spinnaker/deck) is the browser-based UI.
>
> - [Gate](https://github.com/spinnaker/gate) is the API gateway.
>
>   The Spinnaker UI and all api callers communicate with Spinnaker via Gate.
>
> - [Orca](https://github.com/spinnaker/orca) is the orchestration engine. It handles all ad-hoc operations and pipelines. Read more on the [Orca Service Overview](https://spinnaker.io/docs/guides/developer/service-overviews/orca) .
>
> - [Clouddriver](https://github.com/spinnaker/clouddriver) is responsible for all mutating calls to the cloud providers and for indexing/caching all deployed resources.
>
> - [Front50](https://github.com/spinnaker/front50) is used to persist the metadata of applications, pipelines, projects and notifications.
>
> - [Rosco](https://github.com/spinnaker/rosco) is the bakery. It produces immutable VM images (or image templates) for various cloud providers.
>
>   It is used to produce machine images (for example [GCE images](https://cloud.google.com/compute/docs/images) , [AWS AMIs](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html) , [Azure VM images](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/classic/about-images) ). It currently wraps [packer](https://www.packer.io/) , but will be expanded to support additional mechanisms for producing images.
>
> - [Igor](https://github.com/spinnaker/igor) is used to trigger pipelines via continuous integration jobs in systems like Jenkins and Travis CI, and it allows Jenkins/Travis stages to be used in pipelines.
>
> - [Echo](https://github.com/spinnaker/echo) is Spinnaker’s eventing bus.
>
>   It supports sending notifications (e.g. Slack, email, SMS), and acts on incoming webhooks from services like Github.
>
> - [Fiat](https://github.com/spinnaker/fiat) is Spinnaker’s authorization service.
>
>   It is used to query a user’s access permissions for accounts, applications and service accounts.
>
> - [Kayenta](https://github.com/spinnaker/kayenta) provides automated canary analysis for Spinnaker.
>
> - [Keel](https://github.com/spinnaker/keel) powers [Managed Delivery](https://spinnaker.io/docs/guides/user/managed-delivery) .
>
> - [Halyard](https://github.com/spinnaker/halyard) is Spinnaker’s configuration service.
>
>   Halyard manages the lifecycle of each of the above services. It only interacts with these services during Spinnaker startup, updates, and rollbacks.
