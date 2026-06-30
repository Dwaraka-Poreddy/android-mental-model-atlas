# Production Code

In the previous document,

we learned that a Converter Factory translates data between JSON and Kotlin objects.

Let's see what this looks like in a real Android application.

---

## Where Does It Come From?

A natural question appears.

Is a Converter Factory part of Android itself?

No.

Converter Factories are provided by serialization libraries.

Some of the most common ones you'll encounter are:

- Gson
- Moshi
- kotlinx.serialization

Applications choose one of these libraries and register it with Retrofit.

Retrofit then delegates all JSON conversion to that library.

---

## A Typical Retrofit Configuration

One of the most common lines you'll encounter in a production codebase is:

```kotlin
val retrofit = Retrofit.Builder()
    .baseUrl(BASE_URL)
    .client(okHttpClient)
    .addConverterFactory(
        GsonConverterFactory.create()
    )
    .build()
```

This single line tells Retrofit:

> "Whenever you need to convert JSON into Kotlin objects (or vice versa), use Gson."

The same idea applies to every converter library.

Only the implementation changes.

---

## Other Common Converter Libraries

Instead of Gson,

you might encounter:

```kotlin
.addConverterFactory(
    MoshiConverterFactory.create()
)
```

or

```kotlin
.addConverterFactory(
    Json.asConverterFactory(...)
)
```

Although the syntax differs,

their responsibility is identical.

```text
JSON

⇄

Kotlin Objects
```

From Retrofit's perspective,

they all solve the same problem.

---

## Why Doesn't Retrofit Include One By Default?

A natural question appears.

If every application needs JSON conversion,

why doesn't Retrofit simply include one built in?

The reason is flexibility.

Different projects prefer different serialization libraries.

Rather than forcing every application to use the same one,

Retrofit allows developers to choose whichever converter best fits their project.

This keeps Retrofit independent of any particular JSON library.

---

## Recognizing Converter Factories

When exploring an unfamiliar Android project,

you'll often encounter code such as:

```kotlin
.addConverterFactory(...)
```

This tells you that Retrofit is being configured to translate between JSON and Kotlin objects.

The specific converter may vary,

but its responsibility remains the same.

---

## Common Beginner Misconceptions

### "Retrofit parses JSON."

No.

Retrofit delegates JSON conversion to a Converter Factory.

---

### "OkHttp parses JSON."

No.

OkHttp only performs HTTP communication.

It knows nothing about JSON.

---

### "Gson and Moshi are networking libraries."

No.

They are serialization libraries.

Their responsibility is translating data,

not sending network requests.

---

## Production Recognition Checklist

After reading this chapter,

you should immediately recognize the following patterns.

```kotlin
.addConverterFactory(...)
```

↓

A JSON converter is being configured for Retrofit.

---

```kotlin
GsonConverterFactory.create()
```

↓

Retrofit will use Gson for JSON conversion.

---

```kotlin
MoshiConverterFactory.create()
```

↓

Retrofit will use Moshi for JSON conversion.

---

```kotlin
Json.asConverterFactory(...)
```

↓

Retrofit will use kotlinx.serialization for JSON conversion.

---

The specific library may differ from one project to another.

The important thing to recognize is that they all perform the same responsibility:

translating between JSON and Kotlin objects.
