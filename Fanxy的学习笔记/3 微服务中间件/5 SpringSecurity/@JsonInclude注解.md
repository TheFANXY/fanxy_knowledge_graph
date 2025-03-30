# `SpringSecurity`-1-`@JsonInclud`注解

## 1. `@JsonInclude`注解

是`jackSon`中最常用的注解之一，是为实体类在接口序列化返回值时增加规则的注解

例如，一个接口需要 **过滤掉返回值** 为`null`的字段，即值为`null`的字段不返回，可以在 **实体类** 中增加如下注解

```java
@JsonInclude(JsonInclude.Include.NON_NULL)
```

**但是这个前端有时候需要获取为 null 的值做逻辑判断！ 这个注解到底怎么用，用不用，我觉得还是得结合业务怎么搞**



## 2. `@JsonInclude` 注解中的规则

### 2.1. ALWAYS

ALWAYS为默认值，表示全部序列化，即 **默认返回全部字段** ，例：

```java
@JsonInclude(JsonInclude.Include.ALWAYS)
```



#### 2.2. NON_NULL

NON_NULL表示值为null就不序列化，即 **值为null的字段不返回** ，例：

```java
@JsonInclude(JsonInclude.Include.NON_NULL)
```

> 注：1.当实例对象中有`Optional`或`AtomicReference`类型的成员变量时，如果`Optional`或`AtomicReference`引用的实例为`null`，用 `NON_NULL` 不能使该字段不做序列化，此时应使用`NON_ABSENT`规则
>
> `2.Optional`是`java`用来优雅处理空指针的一个特性，一般学过函数式编程和 `Stream` 流的同学应该知道。



### 2.3. NON_ABSENT

`NON_ABSENT`可在实例对象中有`Optional`或`AtomicReference`类型的成员变量时，如果`Optional`或`AtomicReference`引用的实例为null时，也可使该字段不做序列化，同时可以排除值为`null`的字段，例：

```java
@JsonInclude(JsonInclude.Include.NON_ABSENT)
```



### 2.4. NON_EMPTY

`NON_EMPTY`排除字段值为`null`、空字符串、空集合、空数组、`Optional`类型引用为空，`AtomicReference`类型引用为空，例：

```java
@JsonInclude(JsonInclude.Include.NON_EMPTY)
```



#### 2.5. NON_DEFAULT

NON_DEFAULT没有更改的字段不序列化，即未变动的字段不返回，例：

```java
@JsonInclude(JsonInclude.Include.NON_DEFAULT)
```



### 2.6. CUSTOM

相对其他类型，`CUSTOM`略为复杂，这个值要配合`valueFilter`属性一起使用，在序列化的时候会执行`CustomFilter`中的的`equals`方法，该方法的入参就是字段的值，如果`equals`方法返回`true`，字段就不会被序列化，如果`equals`方法返回`false`时字段才会被序列化，即`true`时不返回，false时才返回，例：


```java
@JsonInclude(value = JsonInclude.Include.CUSTOM, valueFilter = CustomFilter.class)
private String field;

static class CustomFilter {
        @Override
        public boolean equals(Object obj) {
            // 为null，或者不是字符串就返回true，即不返回该字段
            if(null == obj || !(obj instanceof String)) {
                return true;
            }

            // 长度大于2就返回true，意味着不被序列化
            return ((String) obj).length() > 2;
        }
    }

```



#### 2.7. USE_DEFAULTS

> 注：注解增加在类名上时，对整个类生效；也可增加在字段上，此时只对该字段生效

如果类注解和字段注解同时存在时，除注解值为USE_DEFAULTS时，均以成员变量注解为准；当值为USE_DEFAULTS的注解在字段上且与类注解同时存在时，以类注解为准，例：

```java
@JsonInclude(JsonInclude.Include.USE_DEFAULTS)
```









