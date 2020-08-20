# Redirecting Method Calls

Redirecting method calls, or just using a `Redirect`, is a way that you can make the JVM call your mixin method instead of the target method at a specified point.
This is handy in situations like when you just want that one `void` to do something else, but don't want to overwrite it or do a head injection and cancellation.
Redirects are very powerful, and can help development a ton.

Here is how you can use a basic redirect to change a `System.out.println` call into a call to your app's logger:

```java
package my.mixins;

import code.TargetClass;
import code.Logger;

import org.spongepowered.asm.mixin.Mixin;
import org.spongepowered.asm.mixin.injection.At;
import org.spongepowered.asm.mixin.injection.Redirect;

@Mixin(TargetClass.class)
public class MixinTargetClass {
    @Redirect(
        // the method this function is called in
        method = "exampleVoid",
        // target the invocation of System.out.println
        at = @At(
            value = "INVOKE",
            target = "Ljava/lang/System;println(Ljava/lang/String;)V"
        )
    )
    private void println(String message) {
        Logger.info(message);
    }
}
```

In this example, we are targeting a void that looks like this:

```java
package code;

public class TargetClass {
    public void exampleVoid() {
        System.out.println("Hey!");
    }
}
```

We can already notice a few things:

1. The redirect accepts the instance of the class being called, but in this instance it is excluded because `println` is a static method.
1. The redirect accepts the arguments given to the method in the `At` (like any mixin with an `At` value of `INVOKE`).
1. The return type of the mixin method matches that of the targeted.

Once you understand this, you get a powerful tool that you can use when you need to, that also saves time, effort, and avoids complexity!
