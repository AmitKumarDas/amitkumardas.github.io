---
layout: post
title: Analysis & Dialysis of design, techniques & architecture
---

## Analysis & Dialysis of design, techniques & architecture

I will try to capture the mood & sentiment of two varying but completing 
worlds i.e. frontend stack & backend technology stack. This will consist of
references to articles & blog posts with some of my own experiences.


## Reverse Engineering st2 web interface design

refer - [st2web](https://github.com/StackStorm/st2web/)

<br />

#### st2-api

- st2-api consists of index.js & api.service.js
  - requires lodash, st2client & urijs
  - logic about connecting, remembering the session, disconnect
  - uses config to extract the host


<br />

#### st2-history-child

- a folder/component that consists of
  - template.html, style.less, index.js, & history-child.directive.js
- index registers the directive & exports it as a angular module
  - export follows a namespace
  - e.g. ```main.modules.st2HistoryChild```
  - registration is done via name of the directive
- directive does the following:
  - sets certain well understood properties
  - sets task name in a ```clever way``` into the scope
  - taskname is in-turn a function that has the core logic
- template does the following:
  - lot of control structures seen in the template
  - especially the ng-if structures

<br />

#### st2-action-reporter

- directive does the following:
  - implements the Reporter & Traceback functions into the scope
  - These scope.Reporter & scope.Traceback functions are closures to link property
  - link seems to be a well understood property by st2web

<br />

#### Summary

- Not so good
  - $event.stopPropagation()
  - this.$apply()
  - Need to learn the property of the objects. Tricky if IDE does not help.
- Tricky parts
  - Usage of bind(this) in code that deals with promise
  - Use of lodash for common validations, or utility stuff

<br />

## Java's move towards being functional

- Lets understand by these sample snippets copied from below link:
  - [refer](https://dzone.com/articles/towards-more-functional-java-using-lambdas-as-pred)
- I am not trying to capture the essence of the logic
- What I will try to do instead is to analyze the diff of an imperative vs functional logic
 
```java

// iterator

public void deleteFromCache(Set<String> deleteKeys) {
    Iterator<Map.Entry<String, Object>> iterator = dataCache.entrySet().iterator();
    while (iterator.hasNext()) {
        Map.Entry<String, Object> entry = iterator.next();
        if (deleteKeys.contains(entry.getKey())) {
            iterator.remove();
        }
    }
}

// vs. for

public void deleteFromCache(Set<String> deleteKeys) {
    for (String deleteKey : deleteKeys) {
        dataCache.remove(deleteKey);
    }
}

// vs. removeIf Predicate (Java 8)

public void deleteFromCache(Set<String> deleteKeys) {
    dataCache.entrySet().removeIf(new Predicate<Map.Entry<String, Object>>() {
        @Override
        public boolean test(Map.Entry<String, Object> entry) {
            return deleteKeys.contains(entry.getKey());
        }
    });
}

// vs. lambda expression (Java 8)

public void deleteFromCache(Set<String> deleteKeys) {
    dataCache.entrySet().removeIf((Map.Entry<String, Object> entry) ->
        deleteKeys.contains(entry.getKey()));
}

// vs. auto inference of arg types of lambda expressions (Java 8)

public void deleteFromCache(Set<String> deleteKeys) {
    dataCache.entrySet().removeIf(entry -> deleteKeys.contains(entry.getKey()));
}
```

<br />

- Above seems a good progress, isn't it.
- For those who have had a dash of Groovy will find this progress very late.
- NOTE - In Groovy above functional coding is a piece of cake.
- Finally, the most important feature the article mentions is all about ```separation of concerns```.
  - i.e. separating **traversal** from **removal** logic
