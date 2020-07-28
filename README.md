
See [this issue](https://github.com/lihaoyi/mill/pull/941) for context.

Project `main` depends on `shapeless_2.13` (see `main/pom.xml`), which pulls `scala-library:2.13.0`. But the dependency management section of its parent has `scala-library:2.11.12` (see `pom.xml` at the root), so the latter version is actually pulled:
```text
$ mvn dependency:build-classpath
…
[INFO] Dependencies classpath:
~/.m2/repository/com/chuusai/shapeless_2.13/2.3.3/shapeless_2.13-2.3.3.jar:~/.m2/repository/org/scala-lang/scala-library/2.11.12/scala-library-2.11.12.jar
…

```

But these overrides are lost for our dependees. Project `dependee` depends on `main` above. This pulls `scala-library:2.13.0`, via `shapeless_2.13`:
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
