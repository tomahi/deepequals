# deepequals
Deep Java object comparison (without the need to implement equals)

* compares two objects by recursively comparing their *properties* (public no-arg methods with non-void return type. The method name does not matter). It (currently) ignores `equals` methods except for some types: primitives and corresponding wrappers, `String`, `LocalDate` and similar, enums, and `Object`.  

* since it compares objects by calling methods, it does not require the objects to be of the same runtime type and thus the user must indicate the top type used for comparison:
  
  ```java
  Foo x = ...;
  Foo y = ...;
  assertTrue(deepEquals(Foo.class, x, y));
  ```
  
* Generics are fully supported by leveraging Guava's [`TypeToken`](http://docs.guava-libraries.googlecode.com/git/javadoc/com/google/common/reflect/TypeToken.html):
  
  ```java
  assertTrue(deepEquals(
          new TypeToken<Supplier<String>>() {},
          () -> "hello",
          new Supplier<String>() {
            @Override public String get() { return "hello"; }
          }));
  ```

* it also natively supports (including all subinterfaces and -classes):
  * `Optional`: deep compares the contained objects recursively when appropriate.
  * `Map`: maps must contain the same keys. Values are deep compared.
  * `Set`: must contain the same elements as indicated by `equals` and `hashCode`. No deep comparison takes place.
  * arrays and `Iterable`: deep compares the elements. Choice of strict or lenient ordering.
  
* supports overriding the way types/properties are compared.
* by default is expects all types involved to be data-carrying objects exclusively i.e. all methods on the objects in the tree both take no args and return something. This restriction can be relaxed, though:

  ```java
  class Foo {
      public void thisMethodIsNotAProperty(/* whatever */) { ... }
      public Bar thisOneIsntEither(Baz x /*, whatever */) { ... }
      public Qux thisIsAProperty() { ... }
  }
  assertTrue(withOptions()
          .typeLenient()
          .deepEquals(Foo.class, x, y));
  ```
  
* other goodies ... Check out the tests for more details.
