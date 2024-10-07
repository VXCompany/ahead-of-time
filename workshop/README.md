# Workshop Notes

[Workshop voor Windows](https://quarkus.io/quarkus-workshops/super-heroes/variants/os-windows-ai-false-azure-false-cli-false-container-true-contract-testing-false-extension-false-kubernetes-true-messaging-false-native-false-observability-false/spine.html)

[Workshop voor macOS](https://quarkus.io/quarkus-workshops/super-heroes/variants/os-mac-ai-false-azure-false-cli-false-container-true-contract-testing-false-extension-false-kubernetes-true-messaging-false-native-false-observability-false/spine.html)

[Workshop voor Linux](https://quarkus.io/quarkus-workshops/super-heroes/variants/os-linux-ai-false-azure-false-cli-false-container-true-contract-testing-false-extension-false-kubernetes-true-messaging-false-native-false-observability-false/spine.html)



## Preparing for the Workshop

### Download the workshop scaffolding

Je kunt de workshop guidance volgen, maar er staat ook een voorbereiding op GitHub klaar die je kunt clonen.

```bash
git clone https://github.com/VXCompany/ahead-of-time.git
cd workshop/quarkus-super-heroes/super-heroes
```

| Let op: Paden in het workshop script zijn niet overal consistent / juist. Daar waar soms quarkus-workshop-super-heroes staat, wordt quarkus-super-heroes bedoeld.

| Letop: Mocht je foutmeldingen krijgen die iets zeggen over schrijftoegang tot bestanden ("Unable to write to file) controleer dan of de super-heroes directory niet ReadOnly is.

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

Volg hier de algemene stappen om van een "gewone" Quarkus app een Native Executable te maken:
[Building a Native Image](https://quarkus.io/guides/building-native-image)

Je hebt de keuze om lokaal een Native App te maken, maar je kunt hier ook een containerized build voor gebruiken.
Ik zou kiezen voor het laatste, dat is een stuk minder "gevoelig" ;)

Mocht je wel lokaal willen bouwen, zorg dan voor een lokale installatie van GraalVM. Deze VM kun je gewoon naast je andere JVM's geinstalleerd hebben.

### GraalVM setup

| Let op: gebruik GraalVM 21 of 23 (reden: build performance)
<https://github.com/graalvm/graalvm-ce-builds/releases>

Graal VM Native Build leunt op de aanwezigheid van een Environment Variable. Deze moet naar de juiste directory wijzen (en mag dus ook afwijken van bijvoorbeeld je JAVA_HOME). Draai je Graal VM ook als gewone JVM, dan ben je in 1x klaar met:

```bash
export GRAALVM_HOME=${JAVA_HOME}
```

Op Windows (Powershell) maak je gebruik van het volgende commando om het pad te controleren:

```bash
$Env:GRAALVM_HOME
```

Mocht deze niet gezet zijn, dan doe je dat via de Advanced Settings.

### Bouw een Native app met GraalVM

Pas de pom.xml van de Villains REST api aan en voeg deze sectie toe die het maken van een native image inschakelt:

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

Maak een native executable met het volgende commando. 
Bouw je lokaal, dan kun je het quarkus.native.container-build argument weglaten.

```bash
./mvnw install -Dnative -DskipTests -Dquarkus.native.container-build=true
```

Juiste versie voor Windows Powershell.
Bouw je lokaal, dan kun je het quarkus.native.container-build argument weglaten.

```bash
./mvnw install "-Dnative" "-DskipTests" "-Dquarkus.native.container-build=true"
```

Dit creeert de normale oputput in de "target" directory, maar ook een uitvoerbaar bestand: ./target/rest-villains-1.0.0-SNAPSHOT-runner.

| Let op: Bij een containerized build is dit echter wel een Linux executable (x86), dus als je dit in Powershell op Windows doet kun je deze niet direct uitvoeren. Doe je dit op WSL, dan werkt het wel.

### Run een containerized Quarkus Native app

Quarkus kan niet alleen een container gebruiken voor de build, maar ook direct een container produceren. Dit maakt het uitvoeren op verschillende platformen een stuk eenvoudiger.

De Quarkus build gebruikt container extensions in het project om dit mogelijk te maken. Voeg er 1 toe aan het Villains project en bouw de app opnieuw.

```bash
./mvnw quarkus:add-extension -Dextensions=container-image-jib
```

Windows Powershell versie:

```bash
./mvnw quarkus:add-extension "-Dextensions=container-image-jib"
```

Nu kun je de Quarkus build vertellen een container met Native app te produceren:

```bash
./mvnw package -Dnative -Dquarkus.native.container-build=true -Dquarkus.container-image.build=true
```

Windows Powershell versie:

```bash
./mvnw package "-Dnative" "-Dquarkus.native.container-build=true" "-Dquarkus.container-image.build=true"
```

Daarna eenvoudig uitvoeren en uittesten met:

```bash
docker images
docker run -p 8080:8080 <imagenaam>/rest-villains:1.0.0-SNAPSHOT
curl http://localhost:8080/api/villains
```

Nu weer terug naar het hoofdpad: [Transactions and ORM](https://quarkus.io/quarkus-workshops/super-heroes/variants/os-windows-ai-false-azure-false-cli-false-container-true-contract-testing-false-extension-false-kubernetes-true-messaging-false-native-false-observability-false/spine.html#rest-transaction-orm)

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
