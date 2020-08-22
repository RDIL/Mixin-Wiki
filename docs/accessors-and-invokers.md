---
title: Accessors and Invokers
---

Accessors and Invokers are ways of gaining access to non-public class members without the pain of needing to use reflection.

# Invokers

Since invokers are a bit more simple, we will take a look at them first. Invokers allow you to *invoke* methods, plain and simple.

To use an invoker, you will want to create a new mixin **even if** you have a mixin for the target class already.

This mixin will be unique, because it will need to be an interface.

It will look like this:

```java
package my.mixins;

import org.spongepowered.asm.mixin.Mixin;
import org.spongepowered.asm.mixin.gen.Invoker;

import code.PrivateApiThing;
import code.Action;

@Mixin(PrivateApiThing.class)
public interface IMixinPrivateApiThing {
    // All that you need to do is name the void `call<function name>`,
    // with the first letter of the function name being uppercase.
    // So for example, an invoker for a void called doAction looks like this:
    @Invoker void callDoAction(Action action);
}
```

(Note: for style reasons, interface mixins are typically named `IMixin<target class name>)`)

Now that we have created this interface mixin, we can use it by casting the target class to the interface:

```java
((IMixinPrivateApiThing) PrivateApiThing).callDoAction(action);
```

# Accessors

Accessors are done in almost the exact same way as invokers, just with a few minor differences:

* The annotation is `org.spongepowered.asm.mixin.gen.Accessor` (`@Accessor`)
* The name of the field annotated with `@Accessor` needs to be either named (`get<target field name>`, `set<target field name>`, or `is<target boolean field name>`)
