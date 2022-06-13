# ServiceTalk Examples

These examples are meant to demonstrate Gradle and Maven build file
configuration outside of the ServiceTalk project. For a full set of
examples see the
[ServiceTalk Repo](https://github.com/apple/servicetalk/tree/main/servicetalk-examples).

This project uses gradle to build and also includes a pom.xml in the gRPC
`helloworld` example to demo how the ServiceTalk gRPC code generation
Maven integration works.

## ServiceTalk BOM
The example Gradle `build.gradle` and Maven `pom.xml` include use of BOM 
([Bill of Materials](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#bill-of-materials-bom-poms))
`pom` and `module` files. BOM filess allow separation of dependency management and version management. Centralizing the
management of versions to a single file greatly reduces the maintenance burden for updating to new versions and improves
consistency because all references to a dependency will use the same version. These files define the versions of modules
which might be included as dependencies, but do not include module as a dependency. Later when the module is included as
a dependency the dependency is specified without including a version and the version specified in the BOM is used.

ServiceTalk includes two BOM files each with slightly different purposes; `servicetalk-bom` and 
`servicetalk-dependencies`. The `servicetalk-bom` BOM includes version information all ServiceTalk modules; version
selection for external non-ServiceTalk dependencies is done in individual modules. The `servicetalk-dependencies` BOM 
extends the `servicetalk-bom` by also defining specific versions to be used for the external modules ServiceTalk depends 
upon. Unless your application requires different versions of these same dependencies then using the 
`servicetalk-dependencies` BOM is the most convenient way to ensure that a tested and supported set of modules is used 
or building and running the application. The `servicetalk-dependencies` BOM should be preferred whenever possible. 

To override a specific dependency version with your application's required version you can use something like the 
following in your `build.gradle`: 

```groovy
implementation(platform("io.servicetalk:servicetalk-dependencies:$servicetalkVersion"))

// Overrides the default Netty BOM version imported by ServiceTalk with a specific version
implementation(enforcedPlatform("io.netty:netty-bom")) {
    version {
        strictly '4.1.76.Final'
    }
}
// Overrides the default Jackson databind module version with a specific version
implementation ("com.fasterxml.jackson.core:jackson-databind") {
    version {
        strictly '2.13.2.1'
    }
}
```

Using the `servicetalk-bom` may be easier when your application wishes to do more complete management of external
dependencies or you wish to include ServiceTalk as an `enforcedPlatform`, but do not want the external
dependencies to be included in that constraint.
