---
layout: post
title: Java SpringBoot Validation 参数校验
date: 2022-01-21
author: 贱子
tags: [Java]
---

SpringBoot Validation使用hibernate validation进行惨参数校验。本文学习其相关概念、关系、使用、配置等知识。

<!--more-->

------

## 0. 参考资料

[Spring Validation最佳实践及其实现原理，参数校验没那么简单！](https://segmentfault.com/a/1190000023471742)

[springboot中参数校验（validation）使用](https://juejin.cn/post/6986645932133580807)

[Spring Boot中validation-api和hibernate-validator详解及快速应用实践，@Valid BindingResult实现接口入参自动检验，Java实体字段校验](https://blog.csdn.net/Hello_World_QWP/article/details/116129788)

## 1. 概念介绍

`Java API`规范(`JSR303`)定义了`Bean`校验的标准`validation-api`，但没有提供实现。`hibernate validation`是对这个规范的实现，并增加了校验注解如`@Email`、`@Length`等。`Spring Validation`是对`hibernate validation`的二次封装，用于支持`spring mvc`参数自动校验。

## 2. 添加依赖

在[springboot官方文档上对validation的使用介绍](https://link.juejin.cn?target=https%3A%2F%2Fdocs.spring.io%2Fspring-boot%2Fdocs%2F2.1.0.RELEASE%2Freference%2Fhtmlsingle%2F%23boot-features-validation)中，说明了只要`JSR-303`实现（例如`Hibernate`验证器）在类路径上，`Bean Validation 1.1`支持的方法验证功能就会自动启用。为此SpringBoot为我们提供了一个 `validation starter`。

如果`spring-boot`版本小于`2.3.x`，`spring-boot-starter-web`会自动传入`hibernate-validator`依赖。如果`spring-boot`版本大于`2.3.x`，则需要手动引入依赖：

```xml
<!--参数校验-->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

## 3. 使用

### 3.1 约束注解

hibernate-validator沿用了validation-api中的所有注解约束，同时也定义了一些自己的约束：

| **constraint**          | **描述**                                                     |
| ----------------------- | ------------------------------------------------------------ |
| **validation-api**      |                                                              |
| @AssertFalse            | 被约束的元素必须是false                                      |
| @AssertTrue             | 被约束的元素必须是true                                       |
| @DecimalMax             | 被约束的元素是数字且其必须小于等于指定值                     |
| @DecimalMin             | 被约束的元素是数字且其必须大于等于指定值                     |
| @Digits                 | 被约束的元素必须是数字且其在约束范围内                       |
| @Email                  | 被约束的元素必须是正确格式的电子邮件地址                     |
| @Future                 | 被约束的元素是未来的时间                                     |
| @FutureOrPresent        | 被约束的元素必须是现在或未来的时间                           |
| @Past                   | 被约束的元素必须是过去的时间                                 |
| @PastOrPresent          | 被约束的元素必须是过去或现在的时间                           |
| @Max                    | 被约束的元素是数字且其必须小于等于指定值                     |
| @Min                    | 被约束的元素是数字且其必须大于等于指定值                     |
| @Negative               | 被约束的元素必须是一个严格的负数（0为无效值）                |
| @NegativeOrZero         | 被约束的元素必须是一个严格的负数（包含0）                    |
| @NotBlank               | 被约束的元素同StringUtils.isNotBlank，只作用在String上，在String属性上加上@NotBlank约束后，该属性不能为null且trim()之后size>0 |
| @NotEmpty               | 被约束的元素同StringUtils.isNotEmpty，作用在集合类上面，在Collection、Map、数组上加上@NotEmpty约束后，该集合对象是不能为null的，并且不能为空集，即size>0 |
| @NotNull                | 被约束的元素不能为null                                       |
| @Null                   | 被约束的元素必须为null                                       |
| @Pattern                | 被约束的元素必须符合自定正则表达式                           |
| @Positive               | 被注释的元素必须严格的正数（0为无效值）                      |
| @PositiveOrZero         | 被注释的元素必须严格的正数（包含0）                          |
| @Size                   | 被约束的元素大小必须介于指定边界（包括）之间                 |
| **hibernate-validator** |                                                              |
| @CodePointLength        | 验证字符序列的编码点长度在min和max之间，可以通过设置规范化策略来验证规范化值 |
| @ConstraintComposition  | 布尔运算符，应用于合成约束注释的所有约束，组合约束注释可以定义组成它的约束的布尔组合，参考ConstraintComposition实现 |
| @CreditCardNumber       | 被注释元素必须是一个有效的信用卡号码，这是Luhn算法的实现，目的是检查用户的错误，而不是信用卡的有效性 |
| @Currency               | 参考moneyaryamount和CurrencyUnit实现                         |
| @EAN                    | 检查被注释的字符序列是否有效，EAN长度为13，支持的类型是String，当字符串为null被认为有效的 |
| @ISBN                   | 检查被注释字符序列是否有效，支持的类型是String，null将被认为有效的，在验证过程中，忽略所有非ISBN字符，所有数字和“X”都被认为是有效的ISBN字符。主要用于证以破折号分隔的ISBN时，这很有用，例如：239-992-190-873-492 |
| @Length                 | 被注释的字符串的长度必须在指定的范围内                       |
| @Range                  | 被注释的元素必须在合适的范围内                               |
| @ScriptAssert           | 类级约束，它对脚本表达式求值注释的元素。此约束可用于实现验证日常活动，依赖于注释元素的多个属性。脚本表达式可以写在任何脚本或表达式语言中，其中的JSR 223兼容的引擎可以在类路径中找到 |
| @UniqueElements         | 验证集合中的每个对象都是唯一的，即不能找到两个相等的元素集合，这对于JAX-RS很有用，它总是将集合反序列化为一个列表。因此，当重复的将其转换为一个集合时，会被隐式或默认的删除掉 |
| @URL                    | 验证带注释的字符串是一个URL，参数protocol、host和port对应URL的相应部分。可以加上一个额外的正则表达式regexp和flags可以进一步定制URL的验证标准。默认情况下，约束验证使用java.net.URL构造函数来验证字符串，这意味着匹配的协议处理程序需要可用，需要保证程序中以下协议的处理程序在默认JVM-HTTP、HTTPS、FTP文件和JAR中存在的 |

### 3.2 `requestBody`参数校验

`POST`、`PUT`请求一般会使用`requestBody`传递参数，这种情况下，后端使用**DTO对象**进行接收。**只要给DTO对象加上`@Validated`注解就能实现自动参数校验**。比如，有一个保存`User`的接口，要求`userName`长度是`2-10`，`account`和`password`字段长度是`6-20`。如果校验失败，会抛出`MethodArgumentNotValidException`异常，`Spring`默认会将其转为`400（Bad Request）`请求。

```java
// 声明参数注解
@Data
public class UserDTO {

    private Long userId;

    @NotNull
    @Length(min = 2, max = 10)
    private String userName;

    @NotNull
    @Length(min = 6, max = 20)
    private String account;

    @NotNull
    @Length(min = 6, max = 20)
    private String password;
  
}

// 请求接口上声明
@PostMapping("/save")
public Result saveUser(@RequestBody @Validated UserDTO userDTO) {
    // 校验通过，才会执行业务逻辑处理
    return Result.ok();
}
```

### 3.3 `requestParam/PathVariable`参数校验

`GET`请求一般会使用`requestParam/PathVariable`传参。如果参数比较多(比如超过6个)，还是推荐使用`DTO`对象接收。否则，推荐将一个个参数平铺到方法入参中。在这种情况下，**必须在`Controller`类上标注`@Validated`注解，并在入参上声明约束注解(如`@Min`等)**。如果校验失败，会抛出`ConstraintViolationException`异常。

```java
@RequestMapping("/api/user")
@RestController
@Validated
public class UserController {
    // 路径变量
    @GetMapping("{userId}")
    public Result detail(@PathVariable("userId") @Min(10000000000000000L) Long userId) {
        // 校验通过，才会执行业务逻辑处理
        UserDTO userDTO = new UserDTO();
        userDTO.setUserId(userId);
        return Result.ok(userDTO);
    }

    // 查询参数
    @GetMapping("getByAccount")
    public Result getByAccount(@Length(min = 6, max = 20) @NotNull String  account) {
        // 校验通过，才会执行业务逻辑处理
        UserDTO userDTO = new UserDTO();
        userDTO.setUserId(10000000000000003L);
        return Result.ok(userDTO);
    }
}
```

### 3.4 分组校验

在实际项目中，可能多个方法需要使用同一个`DTO`类来接收参数，而不同方法的校验规则很可能是不一样的。这个时候，简单地在`DTO`类的字段上加约束注解无法解决这个问题。因此，`spring-validation`支持了**分组校验**的功能，专门用来解决这类问题。还是上面的例子，比如保存`User`的时候，`UserId`是可空的，但是更新`User`的时候，`UserId`的值必须`>=10000000000000000L`；其它字段的校验规则在两种情况下一样。

```java
// 参数注解
@Data
public class UserDTO {

    @Min(value = 10000000000000000L, groups = Update.class)
    private Long userId;

    @NotNull(groups = {Save.class, Update.class})
    @Length(min = 2, max = 10, groups = {Save.class, Update.class})
    private String userName;

    @NotNull(groups = {Save.class, Update.class})
    @Length(min = 6, max = 20, groups = {Save.class, Update.class})
    private String account;

    @NotNull(groups = {Save.class, Update.class})
    @Length(min = 6, max = 20, groups = {Save.class, Update.class})
    private String password;

    /**
     * 保存的时候校验分组
     */
    public interface Save {
    }

    /**
     * 更新的时候校验分组
     */
    public interface Update {
    }
}

//方法注解
@PostMapping("/save")
public Result saveUser(@RequestBody @Validated(UserDTO.Save.class) UserDTO userDTO) {
    // 校验通过，才会执行业务逻辑处理
    return Result.ok();
}
@PostMapping("/update")
public Result updateUser(@RequestBody @Validated(UserDTO.Update.class) UserDTO userDTO) {
    // 校验通过，才会执行业务逻辑处理
    return Result.ok();
}
```

### 3.5 `@Valid`和`@Validated`区别

| 区别         | @Valid                                          | @Validated              |
| ------------ | ----------------------------------------------- | ----------------------- |
| 提供者       | JSR-303规范                                     | Spring                  |
| 是否支持分组 | 不支持                                          | 支持                    |
| 标注位置     | METHOD, FIELD, CONSTRUCTOR, PARAMETER, TYPE_USE | TYPE, METHOD, PARAMETER |
| 嵌套校验     | 支持                                            | 不支持                  |

### 3.6 快速失败(Fail Fast)

`Spring Validation`默认会校验完所有字段，然后才抛出异常。可以通过一些简单的配置，开启`Fali Fast`模式，一旦校验失败就立即返回。

```java
@Bean
public Validator validator() {
    ValidatorFactory validatorFactory = Validation.byProvider(HibernateValidator.class)
            .configure()
            // 快速失败模式
            .failFast(true)
            .buildValidatorFactory();
    return validatorFactory.getValidator();
}
```

