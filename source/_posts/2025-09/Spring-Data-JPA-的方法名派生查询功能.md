---
title: Spring Data JPA 的 “方法名派生查询” 功能
date: 2025-09-10 23:51:59
categories:
    - SpringBoot
tags:
    - SpringDataJPA
---

Spring Data JPA 的“方法名派生查询”功能（Query Method Name Derivation）是在 repository/ 层中使用的，它是 Spring Data JPA 的核心优势之一，可以让你不用写一行 SQL，就能实现数据库查询操作。即：Spring Data 的 派生查询（Derived Query）是一种通过方法名约定自动生成数据库查询的机制，无需手动编写 SQL。

<!-- more -->

Spring Data JPA 的 **“方法名派生查询” 功能（Query Method Name Derivation）** 是在 repository/ 层中使用的，它是 Spring Data JPA 的核心优势之一，可以让你**不用写一行 SQL，就能实现数据库查询操作**。即：Spring Data 的 派生查询（Derived Query）是一种通过方法名约定自动生成数据库查询的机制，无需手动编写 SQL。

## 方法名派生查询功能的使用位置

在 repository/ 里

```java
public interface UserRepository extends JpaRepository<UserEntity, Long> {
    // 这是方法名派生查询的使用位置
}
```

## 方法名解析规则

Spring Data 根据 **方法名的结构** 解析出查询逻辑，生成对应的数据库查询。步骤：

1. **拆分方法名**：以 findBy、existsBy、deleteBy 等关键字为分隔符，提取查询条件。
2. **解析属性路径**：将方法名中的属性名（如 name）映射到实体类的字段。
3. **识别操作符**：通过关键字（如 And、Or、LessThan）确定查询条件的关系和操作。

## 方法命名规范

```
<动词><By><属性名1><关键词><属性名2><排序>
```

举例：

```java
List<User> findByUsernameAndStatusOrderByAgeDesc();
```

**findBy：** 查询动词  
**UsernameAndStatus：** 查询条件字段  
**OrderByAgeDesc：** 排序规则  

## 动词

|  前缀  |  说明  |
|  ----  |  ----  |
|  find...By / get...By / read...By / query...By / search...By / stream...By  |  通用查询方法通常返回储存库类型，`Collection`或`Streamable`子类型或结果包装器，例如`Page`,`GeoResults`或任何其他特定于商店的结果包装器。可用作`findBy...`,`findMyDomainTypeBy...`或其他关键字结合使用。  |
|  exists...By  |  是否存在，返回 boolean  |
|  count...By  |   统计数量  |
|  delete...By / remove...By  |  删除查询方法返回无结果（`void`）或删除计数。  |
|  ...First<number>... / ...Top<number>...  |  将查询结果限制为第一个`<number>`结果。此关键字可以出现在主题的`find`（和其他关键字）和之间的任何位置`by`。  |
|  ...Distinct...  |   使用不同的查询仅返回唯一的结果。查阅特定于商店的文档是否支持该功能。此关键字可以出现在主题的`find`(和其他关键字)和之间的任何位置`by`。  |

## 条件关键字（字段之间连接）

|  关键字  |  说明  |  示例  |
|  ----  |  ----  |  ----  |
|  And  |  且  |  findByUsernameAndStatus  |
|  Or  |  或  |  findByUsernameOrEmail  |
|  Between  |  范围  |   findByAgeBetween(int min, int max)  |
|  LessThan / GreaterThan  |  小于 / 大于  |  findByAgeGreaterThan(int age)  |
|  Before / After  |  时间前 / 时间后  |  findByCreatedAtAfter(Date date)  |
|  IsNull / IsNotNull  |  判空  |  findByEmailIsNull()  |
|  Like  |  模糊匹配（需加 %）  |  findByUsernameLike("%admin%")  |
|  Containing  |   包含（自动加 %）  |  findByUsernameContaining("adm")  |
|  StartingWith / EndingWith  |  前缀 / 后缀匹配  |  findByUsernameStartingWith("a")  |
|  In / NotIn  |   在集合中  |   findByIdIn(List<Long> ids)  |
|  True / False  |  布尔判断  |  findByEnabledTrue()  |

## 排序规则

|  语法  |  示例  |
|  ----  |  ----  |
|  OrderBy<Field>Asc  |   findByStatusOrderByAgeAsc()  |
|  OrderBy<Field>Desc  |  findByStatusOrderByCreatedAtDesc()  |

## 限制返回条数

|  语法  |  示例  |
|  ----  |  ----  |
|  findTop1By...  |   查询符合条件的第一条记录  |
|  findFirst3By...  |   查询前三条  |

## 返回类型

|  返回类型  |  说明  |
|  ----  |  ----  |
|  User  |   查询单个结果（若多条结果会报错）  |
|  Optional<User>  |  单条结果，防止空指针  |
|  List<User>  |   多条结果  |
|  Page<User>  |  分页结果（需传入 Pageable）  |
|  boolean  |  existsBy...  |
|  long  |  countBy...  |
