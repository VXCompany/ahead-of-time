# Workshop Notes

[https://quarkus.io/quarkus-workshops/super-heroes/variants/os-windows-ai-false-azure-false-cli-false-container-true-contract-testing-false-extension-false-kubernetes-true-messaging-false-native-false-observability-false/spine.html](Workshop voor Windows)

## Preparing for the Workshop

### Download the workshop scaffolding

Je kunt de workshop guidance volgen, maar er staat ook een voorbereiding op GitHub klaar die je kunt clonen.

```bash
git clone https://github.com/VXCompany/ahead-of-time.git
cd workshop/quarkus-super-heroes/super-heroes/s
```

| Let op: Paden in het workshop script zijn niet overal consistent / juist. Daar waar soms quarkus-workshop-super-heroes staat, wordt quarkus-super-heroes bedoeld. 

## Villain Microservice

### Bootstrapping the Villain REST Endpoint

| Let op: Versies in de commando's zijn niet altijd correct. Ook zijn ze vrijwel allemaal bedoeld voor Bash en werken ze niet zonder aanpassing in Powershell op Windows.

Juiste versie voor Linux, macOS en Windows Subsystem for Linux

```bash
./mvnw io.quarkus:quarkus-maven-plugin:3.15.1:create \
    -DplatformVersion=3.15.1 \
    -DprojectGroupId=io.quarkus.workshop.superheroes \
    -DprojectArtifactId=rest-villains \
    -DclassName="io.quarkus.workshop.superheroes.villain.VillainResource" \
    -Dpath="api/villains" \
    -Dextensions="rest-jackson"
```

Juiste versie voor Windows Powershell

```bash
./mvnw io.quarkus:quarkus-maven-plugin:3.15.1:create `
    "-DplatformVersion=3.15.1" `
    "-DprojectGroupId=io.quarkus.workshop.superheroes" `
    "-DprojectArtifactId=rest-villains" `
    "-DclassName=io.quarkus.workshop.superheroes.villain.VillainResource" `
    "-Dpath=api/villains" `
    "-Dextensions=rest-jackson"
```

## Sidetrack: de Native Application

### Bouw een Native Application met GraalVM

#### GraalVM setup

Meer info: [https://quarkus.io/guides/building-native-image](Building a Native Executable)

```bash
export GRAALVM_HOME=${JAVA_HOME}
```

Pas de pom.xml aan en voeg deze sectie toe die het maken van een native image inschakelt:

```xml
<profiles>
    <profile>
        <id>native</id>
        <activation>
            <property>
                <name>native</name>
            </property>
        </activation>
        <properties>
            <skipITs>false</skipITs>
            <quarkus.native.enabled>true</quarkus.native.enabled>
        </properties>
    </profile>
</profiles>
```

Maak een native executable met het volgende commando: 

```bash
./mvnw install -Dnative
```

Juiste versie voor Windows Powershell

```bash
./mvnw install "-Dnative"
```

Dit creeert de normale oputput in de "target" directory, maar ook een uitvoerbaar bestand:



## Transactions and ORM

### Installing the PostgreSQL Dependency, Hibernate with Panache and Hibernate Validator

| Let op: Scripts zijn bedoeld voor Bash en werken ze niet zonder aanpassing in Powershell op Windows.

Juiste versie voor Linux, macOS en Windows Subsystem for Linux

```bash
./mvnw quarkus:add-extension -Dextensions="jdbc-postgresql,hibernate-orm-panache,hibernate-validator"
```

Juiste versie voor Windows Powershell

```bash
./mvnw quarkus:add-extension "-Dextensions=jdbc-postgresql,hibernate-orm-panache,hibernate-validator"
```
