---
title: How to use htmlspecialchars() in PHP?
layout: post
comments: true
published: true
description: 
keywords: php, xss
---

The first question is: When to use htmlspecialchars() function?

You use htmlspecialchars EVERY time you output content within HTML, 
so it is interpreted as content and not HTML.

If you allow content to be treated as HTML, you have just opened 
the door to bugs at a minimum, and total XSS hacks at worst.

Now to the next question: How to use htmlspecialchars()?

The problem is that [htmlspecialchars](https://php.net/manual/en/function.htmlspecialchars.php) 
is security relevant but is quite hard to use correctly and safe.

Look at this strange parameter list:

```php
string htmlspecialchars ( string $string [, int $flags = ENT_COMPAT | ENT_HTML401 [, string $encoding = ini_get("default_charset") [, bool $double_encode = TRUE ]]] )
```

The parameter `$flags` is a bitmask and can be combined with at least 10 different flags.

Let's play with this function to find the most secure flags:

1. test: Using the default flags 

```php
echo htmlspecialchars('&"\'<> ', ENT_HTML401 | ENT_COMPAT, 'UTF-8')
```

This result is not secure because the `'` is not encoded to html.

```
&amp;&quot;'&lt;&gt; 
```

2. test: Using the ENT_HTML5 flag

```php
echo htmlspecialchars('&"\'<> ', ENT_HTML5, 'UTF-8')
```

This result is not secure because `"` and `'` is not encoded to html.

```
&amp;"'&lt;&gt; 
```


3. test: Using the ENT_QUOTES | ENT_SUBSTITUTE flags

```php
echo htmlspecialchars('&"\'<> ', ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8')
```

This result is safe because all special characters (`&"'<>`) are converted to HTML entities

```
&amp;&quot;&#039;&lt;&gt; 
```

## Conclusion

The most secure flags for htmlspecialchars are: `ENT_QUOTES | ENT_SUBSTITUTE` in combination with `UTF-8` encoding.

## Helper function

In real life, of course, you don't use htmlspecialchars that way because it's too verbose.

So here is a tiny template helper function for htmlspecialchars.

```php
/**
 * Convert all applicable characters to HTML entities.
 *
 * @param string $text
 * @return string
 */
function html($text)
{
    return htmlspecialchars($text, ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8');
}
```