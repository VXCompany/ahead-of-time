# Setup GraalVM

## All Operating Systems

We need some environment to run our containers. This can be Docker of course, but Podman Desktop is the 100% free alternative.

Please note: Podman on Windows requires a working Windows Subsystem for Linux.

[https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/) or
[https://podman-desktop.io/downloads](https://podman-desktop.io/downloads)


## macOS

### Java 17 (minimum version)

```Bash
brew tap AdoptOpenJDK/openjdk && brew cask install adoptopenjdk17
```

### Maven 3.8.1+

```Bash
brew install maven
```

### GraalVM

[https://www.graalvm.org/latest/docs/getting-started/macos/](https://www.graalvm.org/latest/docs/getting-started/macos/)

## Windows

### Java 17

[https://adoptopenjdk.net/](https://adoptopenjdk.net/)

Make sure you set the JAVA_HOME environment variable and add %JAVA_HOME%\bin to your PATH

### Maven 3.8.1+

[https://maven.apache.org/download.cgi](https://maven.apache.org/download.cgi)

Make sure you set the MAVEN_HOME environment variable and add %MAVEN_HOME%\bin to your PATH

### GraalVM

[https://www.graalvm.org/latest/docs/getting-started/windows/](https://www.graalvm.org/latest/docs/getting-started/windows/)

## Container Image (alternative)

```Bash
docker pull quay.io/rhdevelopers/tutorial-tools
mkdir quarkus-tutorial
cd quarkus-tutorial
mkdir work
docker run -ti -p 8080:8080 -v `pwd`/work:/work \
  -v `mvn help:evaluate -Dexpression=settings.localRepository -q -DforceStdout`:/opt/developer/.m2/repository \
  --rm quay.io/rhdevelopers/tutorial-tools:0.0.7 bash
```

## Linux or WSL (using SDKMAN)

```Bash
  curl -s "https://get.sdkman.io" | bash
  source "$HOME/.sdkman/bin/sdkman-init.sh"
.
  sdk install java
  sdk install maven
  sdk install quarkus
  sdk install java 17.0.8-graalce
  sdk install jbang
```

## Smoketest

Create a simple java file to test both the JVM as the Native Image capability:

```Java
public class HelloWorld {
   public static void main(String[] args) {
      System.out.println("Hello friend, from a Native Image!");
   }
}
```

Compile and run:

```Bash
javac HelloWorld.java
java HelloWorld
```

Compile and build a native executable:

```Bash
native-image HelloWorld
```

And finally execute this executable:

```Bash
helloworld
```

On Windows you can easily measure the difference (although the difference will be hardly evidential):

```Bash
Measure-Command {"java HelloWorld"}
Measure-Command {".\helloworld.exe"}
```
