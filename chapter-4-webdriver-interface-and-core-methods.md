# Chapter 4: WebDriver Interface and Core Methods

## 4.1 WebDriver Interface Overview

- WebDriver is an interface that represents a web browser, which contains abstract methods which is used to perform actions on a browser.
- WebDriver interface extends SearchContext interface which is a parent interface of WebDriver which contains two methods `findElement()` and `findElements()`.
- All browser driver classes implements WebDriver interface, so WebDriver provides common methods required to automate any browser.

## 4.2 Core Methods of WebDriver(I)

- **`void get(String url)`**: Opens webpage of the specified URL in the browser.
- **`String getTitle()`**: Retrieves the title of the current page.
- **`String getCurrentUrl()`**: Fetches the current browser URL.
- **`WebElement findElement(By locator)`**: Returns the first matching web element.
- **`List<WebElement> findElements(By locator)`**: Returns a list of all elements matching the locator.
- **`Options manage()`**: Provides access to window, cookies, logs, and timeouts.
- **`Navigate navigate()`**: For back, forward, refresh, and URL navigation.
- **`void close()`**: Closes the current browser tab.
- **`void quit()`**: Closes all browser windows and ends the session.
