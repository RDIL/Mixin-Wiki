# Mixin Tricks

Here are some handy tricks that can help you write mixins.

## Cast Mixin to Target

If you want to trick Java into thinking that the mixin can be called exactly like the target,
first, make sure that the mixin *extends/implements anything the target class does*, and second,
use this cast:

```java
((TargetClass) (Object) this)
```

Since they both share `Object` somewhere up the class inheritence chain, as well as the using the same abstract classes and interfaces, this will work!
