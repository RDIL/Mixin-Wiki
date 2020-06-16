# Overwriting Methods

There are many different ways you can go about overwriting methods, but before you do, you must ask yourself, "do I really need to overwrite this?".

Overwriting has its upsides:

- Not needing to deal with `CallbackInfo`s
- Not needing to create complex method signature to inject into
- Easier to write

But it also has downsides:

- Less compatibility with other mixins
- Can cause problems if the target method's body changes

So choose wisely!

A good usage for an overwrite would be something like a basic Java getter or setter, where you want your output to be always returned.

Here is an example of that:

```java
package my.app.mixins;

import org.spongepowered.asm.mixin.Mixin;
import org.spongepowered.asm.mixin.Overwrite;

@Mixin(MyTarget.class)
public class MixinMyTarget {
    private String myString = "hello world, I am set in the mixin!";

    @Overwrite
    public String getMyString() {  // the method signature must match exactly!
        return this.myString;
    }
}
```
