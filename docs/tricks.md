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

## Access New Mixin Functions from Other Packages

Anything defined in your mixin package will not be loaded as a class itself. So as far as the runtime is concerned, none of your mixin classes exist after being used as mixins.

This is a problem if you need to access a new method or field you added to a class, because you can't cast anything into a mixin class! The casts will throw NoClassDefFoundErrors at runtime. Any casts within the same class you're casting to are swapped out at runtime so that it works correctly, but that doesn't help for other mixin classes, or other code outside of your mixin package.

To get around this, you can define an interface somewhere else in your code, and have your mixin class implement that interface. That implementation will be merged into the target class at runtime.

Suppose you have this mixin file:

```java
package my.project.mixins;

import org.spongepowered.asm.mixin.Mixin;

/**
 * A mixin for SomeJavaFile.
 */
@Mixin(SomeJavaFile.class)
public class MixinSomeJavaFile {

    // A new function that you're adding to SomeJavaFile
    public void myCoolIdea() {
        System.out.println("I'm running my cool idea!");
    }
}
```

And you want to be able to have the code:

```java
package my.project.other.stuff;
public class Foo {
  void doFoo() {
    SomeJavaFile sjf = new SomeJavaFile();
    sjf.myCoolIdea(); // SomeJavaFile doesn't have any function named myCoolIdea!
  }
}
```

You can't just cast it:

```java
((MixinSomeJavaFile) (Object) sjf).myCoolIdea(); // NoClassDefFoundError: MixinSomeJavaFile is not loaded!
```

Casting only works within the MixinSomeJavaFile class.

The solution is to make a new interface outside of the my.project.mixin package:

```java
package my.project.access;
public interface IMixinSomeJavaFileCoolIdeas {
  // Define myCoolIdea as part of the interface:
  void myCoolIdea();
}
```

```java
package my.project.mixins;

import org.spongepowered.asm.mixin.Mixin;
import my.project.access.IMixinSomeJavaFileCoolIdeas;

/**
 * A mixin for SomeJavaFile. It implements IMixinSomeJavaFileCoolIdeas to expose myCoolIdea().
 */
@Mixin(SomeJavaFile.class)
public class MixinSomeJavaFile implements IMixinSomeJavaFileCoolIdeas {

    // A new function that you're adding to SomeJavaFile, which is now part of a public interface
    @Override
    public void myCoolIdea() {
        System.out.println("I'm running my cool idea!");
    }
}
```

And now you can call the function by casting to this interface:

```java
((IMixinSomeJavaFileCoolIdeas) (Object) sjf).myCoolIdea();
```

## Access New Mixin Fields from Other Packages

The same trick for functions works here, but we can use a getter and a setter instead:

```java
package my.project.access;
public interface IMixinSomeJavaFileCoolIdeas {
  int getMyCoolNumber();
  void setMyCoolNumber(int value);
}
```

And then we can implement the getter and setter in the mixin:

```java
package my.project.mixins;

import org.spongepowered.asm.mixin.Mixin;
import my.project.access.IMixinSomeJavaFileCoolIdeas;

/**
 * A mixin for SomeJavaFile. It implements IMixinSomeJavaFileCoolIdeas to expose myCoolNumber.
 */
@Mixin(SomeJavaFile.class)
public class MixinSomeJavaFile implements IMixinSomeJavaFileCoolIdeas {
    
  private int myCoolNumber;

  @Override
  public int getMyCoolNumber() {
    return myCoolNumber;
  }

  @Override
  public void setMyCoolNumber(int value) {
      myCoolNumber = value;
  }
}
```
