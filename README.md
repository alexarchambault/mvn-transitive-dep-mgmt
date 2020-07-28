
Dependency management overrides versions of transitive dependencies from the project they're defined in:
```text
$ mvn dependency:build-classpath
…
[INFO] Dependencies classpath:
~/.m2/repository/com/chuusai/shapeless_2.13/2.3.3/shapeless_2.13-2.3.3.jar:~/.m2/repository/org/scala-lang/scala-library/2.11.12/scala-library-2.11.12.jar
…

```
(`org.scala-lang:scala-library:2.11.12` is pulled, following depenency management)

But these overrides are lost for our dependees:
```text
$ mvn install
…
[INFO] Installing ~/projects/test/mvn-transitive-dep-mgmt/pom.xml to ~/.m2/repository/test/parent/0.1.0/parent-0.1.0.pom
…
[INFO] Installing ~/projects/test/mvn-transitive-dep-mgmt/main/target/main-0.1.0.jar to ~/.m2/repository/test/main/0.1.0/main-0.1.0.jar
[INFO] Installing ~/projects/test/mvn-transitive-dep-mgmt/main/pom.xml to ~/.m2/repository/test/main/0.1.0/main-0.1.0.pom
…

$ cd dependee
$ mvn dependency:build-classpath
…
~/.m2/repository/test/main/0.1.0/main-0.1.0.jar:~/.m2/repository/com/chuusai/shapeless_2.13/2.3.3/shapeless_2.13-2.3.3.jar:~/.m2/repository/org/scala-lang/scala-library/2.13.0/scala-library-2.13.0.jar
…

$ cs resolve -r m2Local test:main:0.1.0
com.chuusai:shapeless_2.13:2.3.3:default
org.scala-lang:scala-library:2.13.0:default
test:main:0.1.0:default
```
(`org.scala-lang:scala-library:2.13.0` is pulled, *not* following dependency management)
