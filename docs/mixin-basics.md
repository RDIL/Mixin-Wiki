# Mixin Basics

Here is a basic mixin class example:

```java
package my.project.mixins;

import org.spongepowered.asm.mixin.Mixin;

/**
 * A mixin for SomeJavaFile.
 */
@Mixin(SomeJavaFile.class)
public class MixinSomeJavaFile {
}
```

Each class you want to inject/modify will need its own mixin class.

## Injecting into constructors

Now, say we want to run something in the target class's constructor when it is invoked. We can do so like this:

```java
package my.project.mixins;

import org.spongepowered.asm.mixin.Mixin;
import org.spongepowered.asm.mixin.callback.CallbackInfo;
import org.spongepowered.asm.mixin.injection.At;
import org.spongepowered.asm.mixin.injection.Inject;

/**
 * A mixin for SomeJavaFile.
 */
@Mixin(SomeJavaFile.class)
public class MixinSomeJavaFile {

    // signal that we want to inject into a method
    @Inject(
        method = "<init>",  // the jvm bytecode signature for the constructor
        at = @At("RETURN")  // inject at before return opcode (you cannot inject at HEAD for constructor)
    )
    public void constructorHead(
        // you will need to put any parameters the constructor accepts here, they will be the actual passed values
        // it also needs to accept a special argument that mixin passes to this injection method
        CallbackInfo ci
    ) {
        System.out.println("An instance of SomeJavaFile has been created!");
    }

}
```

## Injecting into class methods

You can do something just like it for a regular class method:

```java
package my.project.mixins;

import org.spongepowered.asm.mixin.Mixin;
import org.spongepowered.asm.mixin.callback.CallbackInfo;
import org.spongepowered.asm.mixin.injection.At;
import org.spongepowered.asm.mixin.injection.Inject;

/**
 * A mixin for SomeJavaFile.
 */
@Mixin(SomeJavaFile.class)
public class MixinSomeJavaFile {

    // signal that we want to inject into a method
    @Inject(
        method = "myMethod",  // the method's signature, or just its name
        at = @At("HEAD")  // signal that this void should be run at the method HEAD, meaning the first opcode
    )
    // note that this void here can be named whatever you want, as long as it doesn't conflict with other methods
    // for style reasons, most people just keep the name of this the same as the name of the target method.
    public void myMethodHead(
        // again, put the parameters the method accepts here
        // and also keep this:
        CallbackInfo ci
    ) {
        System.out.println("myMethod was called.");
    }

}
```

And that is it.
