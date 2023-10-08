## 简介

[[Flink(Scala版).pdf#page=3&selection=2,6,4,7|Flink 主要的应用场景]]
- 电商和市场营销
- 物联网
- 物流配送和服务业
- 银行和金融业

[[Lambda 架构]]
![[Lambda 架构.png]]

[[Flink(Scala版).pdf#page=6&selection=122,6,124,5|Flink 的核心特性]]
- 高吞吐和低延迟
- 结果的准确性
- 精确一次（exactly-once）的状态一致性保证
- 可以连接到最常用的存储系统
- 高可用

[[Flink(Scala版).pdf#page=7&selection=106,4,106,18|Flink VS Spark]]  

|          | Flink          | Spark               |
| -------- | -------------- | ------------------- |
| 计算方式 | 流处理         | 微批处理            |
| 数据模型 | 数据流DataFlow | 弹性分布式数据集RDD |

## 快速上手
#### 环境准备
- Windows系统
- [[Java]] 8 或者 11
- [[Scala]] 2.12
- [[IDEA]]
- [[Maven]]
- [[Git]]
- [[log4j]]

#### 项目依赖
[[Flink Java Maven依赖]]

#### WordCount 代码
[[Flink WordCount#批处理| Flink WordCount 批处理代码]]
[[Flink WordCount#流处理| Flink WordCount 流处理代码]]

## 部署

### Flink集群

### 部署模式
#### 会话模式 Session Mode

#### 单作业模式 Per-job Mode

#### 应用模式 Application Mode

### YARN模式


## Flink架构

### 系统架构

### 作业提交流程

## DataStream API

### 执行环境

### 源算子Source

### 转换算子Transformation

### 输出算子Sink


## 时间和窗口

### 时间语义

### 水位线

### 窗口

### 迟到数据的处理


## 处理函数

### 基本处理函数

### 按键分区处理函数

### 窗口处理函数

### 侧输出流

## 多流转换

### 分流

### 基本合流操作

### 基于时间的合流

## 状态编程

### Flink中的状态

### 按键分区状态

### 算子状态

### 状态持久化和状态后端

## 容错机制

### 检查点

### 状态一致性

### 端到端精准一次

## Table API 和 SQL

### 快速上手

### 基本API

### 流处理中的表

### 时间属性和窗口

### 聚合查询

### 联结查询

### 函数

### SQL客户端

### 连接外部系统

## Flink CEP

### 基本概念

### 快速上手

### 模式API

### 模式的检测处理

### CEP的状态机制
