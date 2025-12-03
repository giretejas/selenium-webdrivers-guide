# Chapter 2: Selenium Architecture and Components

## 2.1 Selenium Architecture Overview

Selenium operates using a layered architecture that separates test logic from browser execution, leveraging client libraries and browser-specific drivers. The architecture ensures portability, scalability, and robustness in test automation workflows.

### 2.2 Components of Selenium

- **Selenium IDE**: A record-and-playback tool for rapid prototyping of automation scripts, ideal for learning and proof-of-concept.
- **Selenium WebDriver**: The core API, providing direct control over browsers and supporting multiple programming languages.
- **Selenium Grid**: Facilitates distributed test execution across multiple machines and browsers for parallel and cross-platform testing.
- **Browser Drivers**: Translators between Selenium commands and browser-specific actions (e.g., ChromeDriver, GeckoDriver).

### 2.3 WebDriver Protocol

WebDriver uses HTTP commands defined by the W3C WebDriver specification, enabling language bindings to communicate with browser drivers reliably.

### 2.4 Flow Diagram

```bash
[Your Script] → [WebDriver API] → [Browser Driver] → [Browser]
```

- This flow ensures scripts remain portable across browsers and platforms.

### 2.5 Key Benefits

- Platform-agnostic execution
- Supports Java, Python, C#, Ruby, etc.
- Robust ecosystem and community
