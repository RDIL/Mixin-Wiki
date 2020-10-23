# Overwriting Methods

There are many different ways you can go about overwriting methods, but before you do, you must ask yourself, "do I really need to `@Overwrite` this?".

Overwriting has its upsides:

- Not needing to deal with `CallbackInfo`s, or long `@At`s
- Not needing to create complex method signature to inject into
- Easier to write

But it also has downsides:

- Less compatibility with other mixins/mods
- Can cause problems if the target method's body unexpectedly changes before the overwrite is applied

So choose wisely!

## Example

Here is an example of a simple overwrite for a getter method:

```java
package my.project.mixins;

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
