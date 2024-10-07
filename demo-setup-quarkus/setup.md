# Setup Quarkus

## macOS

```Bash
curl -Ls https://sh.jbang.dev | bash -s - trust add https://repo1.maven.org/maven2/io/quarkus/quarkus-cli/
curl -Ls https://sh.jbang.dev | bash -s - app install --fresh --force quarkus@quarkusio
```

## Windows

```Bash
iex "& { $(iwr https://ps.jbang.dev) } trust add https://repo1.maven.org/maven2/io/quarkus/quarkus-cli/"
iex "& { $(iwr https://ps.jbang.dev) } app install --fresh --force quarkus@quarkusio"
```

## Linux or WSL (using SDKMAN)

```Bash
sdk install quarkus
```

## Smoketest

Create a simple starter application using the CLI:

```bash
quarkus create app org.acme:demo1 --extension='rest'

cd demo1
quarkus dev
```

Alternatively you can also use Maven:

```Bash
mvn io.quarkus.platform:quarkus-maven-plugin:3.9.2:create \
    -DprojectGroupId=org.acme \
    -DprojectArtifactId=demo1 \
    -Dextensions='rest'
   
cd demo1    
./mvnw quarkus:dev
```
