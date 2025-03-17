# Buy_From_Me

Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Flutter** for mobile, while the backend leverages **Firebase** for authentication and real-time features. As part of the app’s functionality, the team integrated **WebViews** to display external content such as terms and conditions, promotional offers, and other pages from external URLs.

The app uses the **flutter_webview_plugin** to display web pages, enabling users to interact with external sites directly within the app. Everything seemed to work fine during development. Users were interacting with the WebView smoothly, and the app passed its security reviews. The team was thrilled with the result, and the app was deployed to production.

## The Trauma: Strange User Complaints and a Growing Nightmare

### Stage 1: Unexpected Redirects
Users started reporting strange behavior when accessing external links inside the app:
- They claimed they were being redirected to unfamiliar websites or login pages.
- The team investigated the routing and logic of the WebView but found no immediate issues.
- Internal testing showed the app functioning perfectly.
- The complaints remained sporadic at first, so the issue was dismissed as an isolated incident.

### Stage 2: Session Hijacking and Data Theft
As reports escalated, the situation became more alarming:
- Some users reported that their session tokens and other sensitive information were stolen after interacting with a WebView.
- Attackers were potentially exploiting the app's WebView functionality to inject malicious scripts.
- The team realized this wasn’t a user error or an isolated bug—it was a **critical vulnerability** stemming from improper WebView usage.

## The Vulnerability: Insecure WebView Usage and Its Exploit

WebViews are powerful components that allow apps to load external web pages. However, if not handled correctly, they can become a **gateway for attacks**. Here’s how the vulnerability was exploited:

### WebView Configuration
- The app used **flutter_webview_plugin** to load external content.
- However, the app **didn’t properly handle URLs and JavaScript settings**, leaving the WebView vulnerable to malicious content injection.

### Lack of URL Validation
- The app **did not validate URLs** before loading them into the WebView.
- Malicious actors could easily exploit this to load harmful websites inside the app.

### JavaScript Injection
- The WebView **did not have JavaScript disabled** when unnecessary.
- The app **failed to restrict access to sensitive actions**, such as cookie or session token theft.

## The Exploit: Attackers Injecting Malicious JavaScript

### How the attack unfolded:
1. **Malicious Website**: An attacker discovered that the app was vulnerable to loading untrusted URLs and injected malicious JavaScript code into one of the external pages displayed inside the WebView.
2. **JavaScript Payload**: The injected JavaScript was designed to capture sensitive information, such as session tokens or user credentials, and send them to an external server controlled by the attacker.
3. **Session Hijacking**: Once the session token was stolen, the attacker could impersonate the victim and perform **unauthorized actions** in the app—such as making purchases or updating personal details.

## The Lesson: Secure WebView Usage

To prevent this kind of vulnerability, follow these **best practices** when using WebViews in mobile apps, especially in Flutter:

### 1. Validate URLs Before Loading
Always validate the URLs before loading them in a WebView. Never load arbitrary or untrusted URLs.

```dart
if (url.startsWith('https://buyfromme.com')) {
  webviewPlugin.launch(url);
} else {
  // Reject unsafe URLs
  print('Unsafe URL');
}
```

### 2. Disable JavaScript if Not Needed
Disable JavaScript unless absolutely necessary. JavaScript in WebViews can lead to **cross-site scripting (XSS) attacks**.

```dart
WebviewScaffold(
  url: "https://buyfromme.com/terms",
  javascriptChannels: Set.from([JavascriptChannel(
    name: 'Toaster',
    onMessageReceived: (JavascriptMessage message) {
      print(message.message);
    },
  )]),
  clearCache: true,
  clearCookies: true,
);
```

### 3. Implement URL Whitelisting
Only allow WebViews to load content from **trusted sources**.

```dart
if (url.contains('buyfromme.com')) {
  _webViewController.loadUrl(url);
} else {
  // Block URLs that don't match the whitelist
  print('Blocked non-whitelisted URL: $url');
}
```

### 4. Use Secure WebView Plugins
Choose **Flutter WebView plugins** that follow security best practices. `webview_flutter` is a **more secure alternative**.

```dart
WebView(
  initialUrl: 'https://buyfromme.com/terms',
  javascriptMode: JavascriptMode.disabled,  // Disable unnecessary JS
  onPageStarted: (url) {
    if (!url.startsWith('https://buyfromme.com')) {
      print('Invalid URL');
    }
  },
);
```

### 5. Avoid Exposing Sensitive Data to JavaScript
Do **not** expose sensitive data (like session tokens) directly in the WebView context.
- Keep sensitive data **secure on the server side**.
- Use **HTTPS** to transmit data securely.

### 6. Secure WebView Configuration
Use the WebView's built-in capabilities to prevent **clickjacking**, **JavaScript injection**, and **other web vulnerabilities**.

```dart
WebView(
  initialUrl: "https://buyfromme.com/terms",
  javascriptMode: JavascriptMode.disabled,  // Ensure no JS injection
  onPageFinished: (String url) {
    // Check URL and ensure integrity
    print("Page finished loading: $url");
  },
);
```

## A Word of Caution
For attackers or anyone considering exploiting such vulnerabilities:
- **Hacking is illegal and unethical**. If caught, you are subject to severe penalties.
- **Every action leaves traces**; you may be tracked and held accountable.
- Instead, use this knowledge to **secure your applications** and **educate others**.

## Conclusion
Insecure WebView usage is a **critical vulnerability** that can expose mobile apps to serious security risks. Always be **cautious** when loading external content and follow **security best practices** to prevent JavaScript injection and session hijacking. By **validating URLs**, **disabling unnecessary JavaScript**, and **configuring your WebView securely**, you can mitigate potential attacks and protect your users.

### Happy Coding, and Secure Your Apps!

