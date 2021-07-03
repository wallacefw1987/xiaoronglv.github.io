---
title: terraform.tfvars 与 variables.tf 区别
layout: post
guid: g807pQj0Yh1ztO35IK4U
date: 2021-06-30
coffee:
tags:
  -
---


Terraform 项目中会定义两个与变量相关的文件：variables.tf 和 terraform.tfvars。

他们之间有什么区别？

## variables.tf

variables.tf 用来定义变量，比如变量名，类型，有点像写代码时函数的参数定义。

在以下范例文件中我定义了两个变量 project 和 environment，他们的类型是 string。

```
# variables.tf
# Input variable definitions

variable "project" {
  description = "project name"
  type = string
}

variable "environment" {
  description = "the environment of project, e.g. production, sandbox, staging"
  type = string
}
```


## terraform.tfvars

terraform.tfvars 类似于一个 `.env` 文件，保存了一些 key-value，用来批量给变量赋值。


```
project = "paylocity"
environment = "sandbox"
```


## 参考资料

1. [Stack Overflow: terraform.tfvars vs variables.tf difference [duplicate]](https://stackoverflow.com/questions/56086286/terraform-tfvars-vs-variables-tf-difference)
2. [Stack Overflow: What is the difference between variables.tf and terraform.tfvars?](https://stackoverflow.com/questions/55959202)
3. [Stack Overflow: Terraform: how to declare a variable as required (or optional) in variables.tf?)(https://stackoverflow.com/questions/68187296/terraform-how-to-declare-a-variable-as-required-or-optional-in-variables-tf/68187417#68187417)