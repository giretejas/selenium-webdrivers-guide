# Chapter 5: Locating Elements – Strategies and Implementation

---

## 5.1 Introduction

- Locators are used to identify an element (buttons, inputs, links) on a web page with the help of locator types defined in a `By` class so selenium can interact with them.
- To identify an element present on a web page we used `findElement()` method which is defined in `WebDriver` interface.
- `findElement()` method will identify element with the help of `By` class static methods known as `Locators`.

---

## 5.2 Locator Types

### 5.2.1  By id(String id)

- A By which locates elements by the value of the `id` attribute.
- Id are unique for each elements on webpage, so its common fastest and stable way to find element using id attribute

```java
driver.findElement(By.id("elementID"));
```

### 5.2.2  By name(String name)

- A By which locates elements by the value of the `name` attribute.
- If web page have multiple element with same name attribute value then method will return first element of page.

```java
driver.findElement(By.name("elementName"));
```

### 5.2.3  By className(String className)

- A By which locates elements by the value of the `class` attribute.
- works only when class name is single, not multiple values.

```java
driver.findElement(By.className("class"));
```

### 5.2.4  By tagName(String tagName)

- A By which locates elements by their `tag` name.

```java
driver.findElement(By.tagName("tagName"));
```

### 5.2.5  By linkText(String linkText)

- A By which locates A elements by the exact text it displays.
- locates a hyperlink(`<a>`) using complete visible text.
- partial text cannot find element
- need same text as given in hyperlink

```java
driver.findElement(By.linkText("linkText"));
```

### 5.2.6  By partialLinkText(String linkText)

- A By which locates elements that contain the given link text.
- Selects a hyperlink that contains partial visible text.
- usefull when text is too long.

```java
driver.findElement(By.partialLinkText("linkText"));
```

### 5.2.7 By cssSelector(String cssSelector)

- A By which locates elements by CSS attributes like `id, name, class` etc.
- css selector are faster than xpath, often more readable.

```java
driver.findElement(By.cssSelector("selector"));
```

#### following ways to find element using css selector

**1. id**:

```java
driver.findElement(By.cssSelector("#id"));
```

**2. class**:

```java
driver.findElement(By.cssSelector(".class"));
```

**3. attribute**:

```java
driver.findElement(By.cssSelector("[attribute=value]"));
```

**4. multiple attribute**:

```java
driver.findElement(By.cssSelector("tagName[attribute1=value1][attribute2=value2]"));
```

**5. contains**:

```java
driver.findElement(By.cssSelector("tagName[attribute*=value]"));
```

**6. starts-with**:

```java
driver.findElement(By.cssSelector("tagName[attribute^=value]"));
```

**7. ends-with**:

```java
driver.findElement(By.cssSelector("tagName[attribute$=value]"));
```

---

## 5.3 By xpath(String xpathExpression)

- A By which locates elements via XPath.
- It is most powerfull and flexible locator strategy, generally used when element doesn't have id, name, class attributes.
- It supports dynamic xpath, parent-child traversal condition, indexing, logical operators etc.

### 5.3.1 Types of xpath expression

