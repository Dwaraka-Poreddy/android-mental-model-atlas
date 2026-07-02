# Deep Linking — Production Code

## Purpose

Understand how Deep Links are implemented in Android applications and learn to recognize the different Deep Linking mechanisms used in production code.

---

## Looking Back

In the previous chapter, we learned that Deep Linking allows navigation to begin at a specific destination from outside the application.

In production Android applications, Deep Links can originate from many different sources and are configured using several Android platform features.

This chapter focuses on recognizing those implementations.

---

# Recognition

When exploring a production Android project, you'll commonly encounter:

```xml
<intent-filter>
```

```xml
<data
    android:scheme="https"
    android:host="shop.example.com"/>
```

```kotlin
navDeepLink { ... }
```

```kotlin
Intent.ACTION_VIEW
```

```kotlin
PendingIntent
```

These are all commonly associated with Deep Linking.

---

# Pattern 1 — URI Schemes

The simplest Deep Links use a custom URI scheme.

For example,

```text
myapp://product/42
```

When Android encounters this URI,

it can launch your application directly.

Common use cases include:

- communication between apps,
- internal enterprise applications,
- QR codes,
- development builds.

---

# Pattern 2 — Android App Links

Production applications usually prefer HTTPS-based links.

For example,

```text
https://shop.example.com/products/42
```

These look exactly like normal web URLs.

If the application is installed,

Android can open the app directly.

Otherwise,

the browser opens the website.

This provides a seamless experience for users.

---

# Pattern 3 — Intent Filters

Android needs to know which links belong to your application.

That mapping is declared using Intent Filters.

Typical production code looks like:

```xml
<intent-filter>
    <action android:name="android.intent.action.VIEW"/>

    <category android:name="android.intent.category.DEFAULT"/>
    <category android:name="android.intent.category.BROWSABLE"/>

    <data
        android:scheme="https"
        android:host="shop.example.com"/>
</intent-filter>
```

Think of the Intent Filter as:

> "These kinds of links belong to this application."

---

# Pattern 4 — Compose Navigation

Compose Navigation allows destinations to declare their own Deep Links.

For example,

```kotlin
composable(
    route = "product/{id}",
    deepLinks = listOf(
        navDeepLink {
            uriPattern = "https://shop.example.com/products/{id}"
        }
    )
)
```

The destination can now be reached through both:

- normal in-app navigation,
- external Deep Links.

---

# Pattern 5 — Notifications

Notifications often use Deep Links.

For example,

```text
Your order has shipped.
```

Tapping the notification should open:

```text
Order Details
```

instead of:

```text
Home
```

This is one of the most common production uses of Deep Linking.

---

# Pattern 6 — Email & SMS

Many applications send links through:

- email,
- SMS,
- messaging applications.

Examples include:

```text
Reset Password

Verify Email

Track Order

Accept Invitation

Join Workspace
```

Each link opens the corresponding destination directly.

---

# Pattern 7 — QR Codes

QR codes frequently encode Deep Links.

Examples include:

- restaurant menus,
- event check-ins,
- Wi-Fi setup,
- payments,
- product information.

Scanning the code opens the appropriate destination immediately.

---

# Pattern 8 — Navigation From Other Apps

Applications often launch each other using Deep Links.

Examples include:

- Maps opening a ride-sharing app,
- browsers opening shopping apps,
- payment applications opening banking apps.

The receiving application simply starts at the requested destination.

---

# Testing Deep Links

During development,

Android Studio allows Deep Links to be tested without manually opening browsers or sending notifications.

Production teams frequently test:

- valid links,
- invalid links,
- missing parameters,
- unsupported hosts,
- unsupported schemes.

Deep Link testing is an important part of navigation testing.

---

# Common Production Mistakes

## Registering Multiple Apps For The Same URI

This causes Android to ask users which application they want to open.

Prefer verified App Links whenever possible.

---

## Assuming Every Parameter Exists

External links can be malformed.

Applications should validate incoming data before using it.

---

## Sending Large Objects Through Links

Deep Links should identify resources.

For example,

```text
Product ID = 42
```

not

```text
Entire Product Object
```

The destination should load the remaining data after opening.

---

## Forgetting Authentication

Some Deep Links require authentication.

If the user isn't signed in,

applications typically redirect to Login,

then continue to the original destination after successful authentication.

---

# How To Read Production Code

When you see:

```xml
<intent-filter>
```

read it as:

```text
This application can be launched
for these kinds of links.
```

---

When you see:

```kotlin
navDeepLink { ... }
```

read it as:

```text
This destination can be opened
directly from outside the application.
```

---

When you see:

```text
https://...
```

read it as:

```text
This is an App Link
that may launch the application.
```

---

# Key Takeaways

```text
✓ Deep Links allow navigation to begin outside the application.

✓ URI Schemes and Android App Links are the two most common Deep Linking mechanisms.

✓ Intent Filters tell Android which links belong to your application.

✓ Compose destinations can declare their own Deep Links.

✓ Notifications, emails, SMS, QR codes and other applications commonly use Deep Links.

✓ Always validate incoming parameters.

✓ Deep Links should identify resources, not transport large amounts of data.
```
