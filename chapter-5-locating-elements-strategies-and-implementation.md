# Chapter 5: Locating Elements – Strategies and Implementation

## Introduction

- Locators are used to identify an element (buttons, inputs, links) on a web page with the help of locator types defined in a `By` class so selenium can interact with them.
- To identify an element present on a web page we used `findElement()` method which is defined in `WebDriver` interface.
- `findElement()` method will identify element with the help of `By` class static methods known as `Locators`.

## Locator Types

---

### 1 By id(String id)

- A By which locates elements by the value of the `id` attribute.
- Id are unique for each elements on webpage, so its common fastest and stable way to find element using id attribute

```java
driver.findElement(By.id("elementID"));
```

### 2 By name(String name)

- A By which locates elements by the value of the `name` attribute.
- If web page have multiple element with same name attribute value then method will return first element of page.

```java
driver.findElement(By.name("elementName"));
```

### 3 By className(String className)

- A By which locates elements by the value of the `class` attribute.
- works only when class name is single, not multiple values.

```java
driver.findElement(By.className("class"));
```

### 4 By tagName(String tagName)

- A By which locates elements by their `tag` name.

```java
driver.findElement(By.tagName("tagName"));
```

### 5 By linkText(String linkText)

- A By which locates A elements by the exact text it displays.
- locates a hyperlink(`<a>`) using complete visible text.
- partial text cannot find element
- need same text as given in hyperlink

```java
driver.findElement(By.linkText("linkText"));
```

### 6 By partialLinkText(String linkText)

- A By which locates elements that contain the given link text.
- Selects a hyperlink that contains partial visible text.
- usefull when text is too long.

```java
driver.findElement(By.partialLinkText("linkText"));
```

### 7 By cssSelector(String cssSelector)

- A By which locates elements by CSS attributes like `id, name, class` etc.
- css selector are faster than xpath, often more readable.

```java
driver.findElement(By.cssSelector("selector"));
```

#### following ways to find element using css selector

**A. id**:

```java
driver.findElement(By.cssSelector("#id"));
```

**B. class**:

```java
driver.findElement(By.cssSelector(".class"));
```

**C. attribute**:

```java
driver.findElement(By.cssSelector("[attribute=value]"));
```

**D. multiple attribute**:

```java
driver.findElement(By.cssSelector("tagName[attribute1=value1][attribute2=value2]"));
```

**E. contains**:

```java
driver.findElement(By.cssSelector("tagName[attribute*=value]"));
```

**F. starts-with**:

```java
driver.findElement(By.cssSelector("tagName[attribute^=value]"));
```

**G. ends-with**:

```java
driver.findElement(By.cssSelector("tagName[attribute$=value]"));
```

## 8 By xpath(String xpathExpression)

- A By which locates elements via XPath.
- It is most powerfull and flexible locator strategy, generally used when element doesn't have id, name, class attributes.
- It supports dynamic xpath, parent-child traversal condition, indexing, logical operators etc.

### Types of xpath expression

#### Absolute xpath

- starts from root /
- we need to navigate from root to immidiate child
- can be identify element fastly as we given complete path, but it can be very lengthy and time consuming as well as hard to maintain
- identify tree and maintain is difficult. If html structure changed it will not identify by minor changes aslo, so we need to write again complete path from root.

```java
driver.findElement(By.xpath("/html/body/header/div[3]/div/div[2]/span[3]/b[2]/input[1]"));
```

#### Relative xpath (Recommended)

- Does not starts from root
- can be navigate through parent to child and child to parent also.
- easier to read and maintain

```java
driver.findElement(By.xpath("//input[@id='username']"));
```

#### Basic xpaths

**1. xpath by attribute**:

```java
driver.findElement(By.xpath("//input[@id='username']"));
```

**2. xpath for multiple attribute**:

```java
driver.findElement(By.xpath("//input[@id='username' and @type='text']"));
```

**3. xpath using contains**:

```java
driver.findElement(By.xpath("//input[contains(@id,'username')]"));
```

**4. xpath using starts-with**:

```java
driver.findElement(By.xpath("//input[starts-with(@id,'username')]"));
```

**5. xpath using text**:

```java
driver.findElement(By.xpath("//a[text()='Login']"));
```

**6. xpath using index**:

```java
driver.findElement(By.xpath("(//a[text()='Submit'])[2]"));
```

**7. xpath using index**:

```java
driver.findElement(By.xpath("(//a[text()='Submit'])[2]"));
```

**8. xpath using complex index**:

```java
driver.findElement(By.xpath("(//ui//li[position()=last()-1]"));
```

#### special cases of xpath using conditions, methods

**1. condition based xpath**:

```java
driver.findElement(By.xpath("//button[@id='login' or @name='loginBtn']")); // or

driver.findElement(By.xpath("//input[@type='text' and @name='username']")); // and
```

**2. use not**:

```java
driver.findElement(By.xpath("//input[@name='username' and not(@disabled)]")); // not()
```

**3. use ends-with**:

```java
driver.findElement(By.xpath("//input[substring(@id, string-length(@id) - string-length('end') + 1)='end']")); // ends-with using substring()
```

**4. use contains and text**:

```java
driver.findElement(By.xpath("//label[contains(text(),'Password')]")); // contains partial text
```

**5. use normalize-space**:

- To handle extra space

```java
driver.findElement(By.xpath("//a[normalize-space()='My Profile']")); 
```

**6. use string-length**:

```java
driver.findElement(By.xpath("//input[string-length(@value) > 5']")); 
```

**7. use last**:

- last occurance of element

```java
driver.findElement(By.xpath("//table//tr[last()]")); 
```

**8. use position**:

```java
driver.findElement(By.xpath("//ul//li[position()=3]")); 
```

#### xpath by Axes

- Axes helps to navigate DOM relative to another node

**1. parent::**:

```java
driver.findElement(By.xpath("//input[@id='email']/parent::div")); // Move to Parent div element 
```

**2. child::**:

```java
driver.findElement(By.xpath("//div[@id='form']/child::input")); // Move to child input element 
```

**3. self::**:

```java
driver.findElement(By.xpath("//div[@id='form']//input[@id='email']/self::input")); 
```

**4. ancestor::**:

```java
driver.findElement(By.xpath("//input[@id='email']/ancestor::form")); 
```

**5. ancestor-or-self::**:

```java
driver.findElement(By.xpath("//div[@class='box']/ancestor-or-self::*")); 
```

**6. descendant::**:

```java
driver.findElement(By.xpath("//div[@id='container']/descendant::input")); 
```

**7. descendant-or-self::**:

```java
driver.findElement(By.xpath("//div[@class='box']/descendant-or-self::*")); 
```

**8. following-sibling**:

```java
driver.findElement(By.xpath("//label[@for='username']/following-sibling::input")); 
```

**9. following**:

```java
driver.findElement(By.xpath("//div[@id='username']/following::input")); 
```

**10. preceding**:

```java
driver.findElement(By.xpath("//div[@id='password']/preceding::input")); 
```

**10. preceding-sibling**:

```java
driver.findElement(By.xpath("//input[@id='password']/preceding-sibling::label")); 
```

#### xpath by Operator

**1.Greater than**:

```java
driver.findElement(By.xpath("//tr[position() > 1]")); 
```

**2.Less than**:

```java
driver.findElement(By.xpath("//tr[position() < 1]")); 
```
