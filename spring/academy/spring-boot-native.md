### Spring Aot
  1. Spring Framework 6 now allows you to perform Ahead-Of-Time (AOT) optimizations to precompute at build-time which 
        beans need to be injected, reducing the amount of reflection needed at runtime, making your Spring Boot application easier to analyze by the GraalVM native image compiler.
  2. After the generation of the Java bytecode, the GraalVM native image compiler performs a static analysis of the application, 
       generates an optimized, standalone native executable that can be statically linked and run on top of a minimal operating system, and deploys as a small and efficient container image, without the need for a JVM installation.

- the native image compiler analyzes your application, tries to identify all code used, and removes any code which is not actually used by the application. 
   This is sometimes referred to as “dead code detection and removal”. It doesn’t remove application code, but rather any unused methods from library dependencies.
    Removing unused code has many benefits, such as a smaller memory footprint when running the application, and a faster compilation time than would otherwise be possible.

#### Native Runtime Hints (from spring academy without editing)
- Java is a statically typed language, but some operations - like reflection, resources, proxies, and serialization - are dynamic (performed at runtime). GraalVM refers to the set of data that is required to perform these dynamic operations as Reachability Metadata. Like application code itself, GraalVM attempts to only include the necessary reachability metadata, in order to produce a small executable in a reasonable amount of time.

- It’s not possible - or, it's very difficult - for the compiler to know which reachability metadata is required at runtime, and which is not. Therefore, the application programmer needs to provide some information in the application code so that all required elements are included in the compiled native image. The Spring API used to provide this information is called the Runtime Hints API. Why is the word “hints” used? Because the programmer is telling (hinting) to the compiler that additional information needs to be compiled into the runtime image.

- Providing hints requires additional programming work, so the Spring Framework and GraalVM automatically provide some reachability metadata, so that you don’t have to! Which metadata are automatically provided? There are two main types:
  `build/generated/aotTestResources/META-INF/native-image/`
  1. Metadata exposed on Spring Beans API and requiring reflection or proxies in the application code is automatically included in the native image. For example, if a class is included in a Repository bean as a return value, then the reflection metadata for that class is included.
  2. In addition to including entries based on your beans API, the Native Build Tools plugin is automatically configured to retrieve reachability metadata for supported Open Source libraries from https://github.com/oracle/graalvm-reachability-metadata. You can get a more approachable list of the supported libraries in https://www.graalvm.org/native-image/libraries-and-frameworks/. This is the mechanism by which anyone who develops a Java library can make it easy to use in applications deployed as a native image.
- By inspecting your own application’s Beans API and combining that with the open source reachability metadata, the GraalVM generates much of the reachability metadata that the native image needs to run correctly. Thanks to that information, most of your application can compile to a native executable without extra work on your part.

- On the other hand, developers do need to provide hints for any items (like classes, or resources) on which runtime operations are performed, including reflection, dynamic proxying, or serialization, and which aren't automatically detected by the native image build tools, or Spring Ahead-Of-Time (AOT) transformations.

#### Ahead-of-Time Optimizations and Reachability Metadata
- The application source code contains hints which allow the Spring Ahead-of-Time Optimizations (AOT) phase to generate reachability metadata. These hints aren't separate from the application, they're actually an integral part of the application source code.
- During the Compile (native image) phase, this reachability metadata is used to add the necessary data to the compiled, standalone native image.

- It’s worthwhile to understand the difference between the generation of the reachability metadata (during javac compilation), the actual inclusion of the required data in the native image (done by the native image build tools), and the benefit this separation provides:
  - The metadata doesn’t affect a regular JVM deployment, but can be critical for a native image compilation and deployment.
  - Incorporating the native hints in the application source code ensures that the application can be deployed both as a traditional JVM-deployed Java application, or as a native image application.


#### Advantages :
- Instant startup: With typical startup time measured in milliseconds instead of seconds for the JVM.
- No warmup: Peak performance is available immediately.
- Low resource usage: You can run your workload on cheap instances with small CPU and low memory.
- Reduced surface attack: One drawback of native images is that you have to explicitly declare at build time which reflection and serialization operations will be performed at runtime. However, this becomes an advantage in terms of security, for two reasons:
  - The application contains only the necessary reachability metadata (which is also mentioned in the tradeoffs section, below), reducing the data available to attackers.
  - The application is in a "closed world", with no capabilities to load new code.
- Compact packaging: Smaller containers are easier to deploy and store.

#### Drawbacks:
- Native image build time is significantly slower and more resource consuming than a typical JVM build, and is performed
  in addition to the JVM build, not instead-of.
- Native images require additional reachability metadata that introduce additional compatibility requirements. 
  Spring does its best to infer most of it, but for real-world applications, you'll likely have to provide additional hints.
- There's no dynamic optimization done at runtime, as is done with the JVM JIT compiler. Additionally, the level of 
  performance of native images depends on the distribution of GraalVM used. The bottom line is: With enough resources, the JVM usually provides somewhat higher throughput than native images.


##### Notes from course labs:
- Note (Recapping some learnings from the previous lesson): In this specific case, the Spring Ahead-Of-Time (AOT) engine can’t infer this information, 
  which is needed by the Thymeleaf templating library. Therefore, you'll need to specify it explicitly. Thymeleaf is not a special case. 
  Any reflection-based serialization - for example, in HTTP handler methods (when using HTTP clients like WebClient or RestTemplate) - will also require 
  you to provide hints in the same way. Thus, this solution applies to many real-world scenarios you'll encounter when preparing an application for deployment as a native image.
- The @RegisterReflectionForBinding annotation accepts a list of classes, and ensures that the necessary reflection metadata for these classes are available to the running native image. 
  - ex: @RegisterReflectionForBinding({ CashCardDto.class, UserDto.class })
- the native compilation does not automatically include all resources. Doing so would be wasteful (one reason for this is that not all resources are required at runtime). 
   You need to provide a hint to specify which resources should be available at runtime.
```java
@SpringBootApplication
@RegisterReflectionForBinding({ CashCardDto.class, UserDto.class })
@ImportRuntimeHints(CashCardApplication.Hints.class)
public class CashCardApplication {
    
    static class Hints implements RuntimeHintsRegistrar {
        @Override
        public void registerHints(RuntimeHints hints, @Nullable ClassLoader classLoader) {
            hints.resources().registerPattern("cashcard-banner.txt");
        }
    }
}
```

