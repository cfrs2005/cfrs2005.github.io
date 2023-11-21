---
title: Quick Start with SequenceDiagram Syntax
date: 2023-11-21 10:00:00 +0800
categories: [ Technology,Beginner,Kubernetes, Cloud Computing and DevOps ]
tags: [ Software Development ]
img_path: '/assets/img/202311/'
image:
  path: SequenceDiagram.png
  alt: Learn the fundamental syntax of SequenceDiagram quickly with this tutorial
---

Learn the fundamental syntax of SequenceDiagram quickly with this tutorial. Explore how to create interaction diagrams, customize them, and represent object interactions using SequenceDiagram, a powerful tool in UML diagramming.



## Install Sequence Diagram

```shell
npm install -g sequence-diagram
```

## DemoFirst

![SequenceDiagramD1.png](SequenceDiagramD1.png)

```shell
sequenceDiagram
title Search Book : Use Case

participant Customer
participant SearchPage as "Search Page"
participant SearchResultsPage as "Search Results Page"
participant Catalog
participant SearchResults as "Search Results"

Customer->>SearchPage: onSearch(author)
Note right of SearchPage: The system validates the\nCustomer's search criteria.

alt author entered
    SearchPage->>Catalog: searchByAuthor(author)
    Catalog->>SearchResults: create()
    SearchResults->>SearchResultsPage: display()
else author not entered
    SearchPage->>SearchResultsPage: displayErrorMessage()
end

Note over SearchResultsPage: When the search is complete,\nthe system displays the search results on\nthe Search Results page.

```

## Annotate

```shell
对象1->对象2: 操作1: 这是一个注释
对象2-->对象1: 操作2: 另一个注释
```

## Message Styles

| 标记   | 描述         |
|:-----|:-----------|
| ->	  | 无箭头实线      |
| -->  | 	无箭头虚线     |
| ->>  | 	有箭头的实线    |
| -->> | 	有箭头的虚线    |
| -x	  | 终点为 x 的实线  |
| --x  | 	终点为 x 的虚线 |

## Warp/NewLine

```shell
sequenceDiagram
a->>b:visit site\nb.80aj.com
```

## Use Actor

```shell
actor 用户 as 用户
对象1->用户: 请求登录
用户->对象2: 提供凭据
对象2-->用户: 认证结果
```

## SetColor

```shell
对象1->对象2: 操作1 #FF5733
对象2-->对象1: 操作2 #3498DB
```

## More

- [sequence-diagram使用介绍](https://pintorajs.vercel.app/zh-CN/docs/diagrams/sequence-diagram/)
- [What is Sequence Diagram?](https://www.visual-paradigm.com/guide/uml-unified-modeling-language/what-is-sequence-diagram/)
