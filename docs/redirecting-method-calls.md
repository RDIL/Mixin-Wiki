# Redirecting Method Calls

Redirecting method calls, or just using a `Redirect`, is a way that you can make the JVM call your mixin method instead of the target method at a specified point.
This is handy in situations like when you just want that one `void` to do something else, but don't want to overwrite it or do a head injection and cancellation.
Redirects are very powerful, and can help development a ton.

Here is how you can use a basic redirect:

```java
package my.cool.thing.mixins;

import some.TargetClass;

import org.spongepowered.asm.mixin.Mixin;
import org.spongepowered.asm.mixin.injection.At;
import org.spongepowered.asm.mixin.injection.Redirect;

@Mixin(TargetClass.class)
public class MixinTargetClass {
    @Redirect(method = "exampleVoid", at = @At(value = "INVOKE", target = "Lsome/OtherClass;doStuff(Ljava/lang/String;I)I"))
    private int getSomeData(OtherClass otherClass, String myString, int myInt) {
        return myString.length * myInt;
    }
}
```

In this example, we are targeting a void that looks like this:

```java
package some;

public class TargetClass {
    public void exampleVoid() {
        OtherClass oc = new OtherClass();
        oc.doStuff("hello world", 5);
    }
}
```

(with the `OtherClass` looking like this):

```java
package some;

public class OtherClass {
    public int doStuff(String myString, int myInt) {
        return myString == "hello" ? 5 : 4;
    }
}
```

We can already notice a few things:

1. The redirect accepts the instance of the class being called, in this case, it would be the OtherClass instance being used in the target method.
1. The redirect accepts the arguments given (like any mixin with an `At` value of `INVOKE`).
1. The return type of the mixin method matches that of the targeted.

Once you understand this, you get a powerful tool that you can use when you need to, that also saves time, effort, and avoids complexity!
