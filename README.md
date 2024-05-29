# Notizen P2

[M347-P2-Lernziele.pdf (62.3 kB)](Files/M347-P2-Lernziele.pdf) 

[https://gbssg.gitlab.io/m347/compose-docker-compose.yaml/#container\_name](https://gbssg.gitlab.io/m347/compose-docker-compose.yaml/#container_name)  

  
Lernziele  

## Dockerfile

### ▪ Sie verstehen den Einsatz von Multistage-Containern

###   

### ▪ Sie erstellen einen Multistage-Container

###   

### ▪ Sie finden Fehler in einem Dockerfile

  

  

## YAML

### ▪ Sie verstehen den Aufbau eines YAML-Files

###   

### Docker compose

###   

### ▪ Sie kennen den Aufbau und die wichtigsten Schlüsselwörter (image, build, container\_name, restart,

  

### environment, volumes, ports, expose, networks, secrets) eines docker-compose.yaml

|     |     |
| --- | --- |
| <br> | <br> |
| ```<br>image: my_service_image:latest<br>```<br><br>  <br><br><br><br> | **`image`**: Gibt das Docker-Image an, das für den Container verwendet werden soll. Wenn das Image nicht lokal vorhanden ist, wird es von Docker Hub heruntergeladen<br> |
| ```<br> build: [verzeichniss]<br>```<br><br><br> | **`build`**: Gibt an, dass das Image aus einem Dockerfile im angegebenen Verzeichnis gebaut werden soll.<br> |
| ```<br>container_name: my_service_container<br>```<br><br>  <br><br><br><br> | **`container_name`**: Setzt den Namen des Containers.<br> |
| ```<br>restart: always<br>```<br><br>  <br><br><br><br> | **`restart`**: Definiert die Restart-Policy für den Container. Mögliche Werte sind `no`, `always`, `on-failure`, und `unless-stopped`.<br> |
| ```<br>environment:<br>  - ENV_VAR1=value1<br>  - ENV_VAR2=value2<br>  - MYSQL_PASSWORD_FILE: /run/secrets/db_password<br>```<br><br>  <br><br><br><br> | **`environment`**: Setzt Umgebungsvariablen für den Container<br> |
| ```<br>volumes:<br>  - ./data:/data<br>```<br><br>  <br><br><br><br> | **`volumes`**: Bindet Volumes in den Container ein. Dies kann ein Verzeichnis auf dem Host oder ein benanntes Volume sein.<br> |
| ```<br>ports:<br>  - "8080:80"<br>```<br><br>  <br><br><br><br> | **`ports`**: Veröffentlicht Ports, damit der Dienst von außen zugänglich ist. Das Format ist `<host_port>:<container_port>` |
| ```<br>expose:<br>  - "8080"<br>```<br><br>  <br><br><br><br> | **`expose`**: Macht Ports nur für andere Dienste innerhalb des gleichen Netzwerks verfügbar.<br> |
| ```<br>networks:<br>  - my_network<br>```<br><br>  <br><br><br><br> | **`networks`**: Verbindet den Dienst mit einem oder mehreren Netzwerken.<br> |
| ```<br>secrets:<br>  - my_secret<br>``` | **`secrets`**: Listet die Docker Secrets auf, die dem Container zur Verfügung gestellt werden.<br> |
| ```<br>secrets:<br>  my_secret:<br>    file: my_secret.txt<br>```<br><br><br> | Definiert die Dateien, die als Docker Secrets verwendet werden sollen.<br> |
| ```<br>volumes:<br>  data_volume:<br>    driver: local<br>```<br><br>  <br><br><br><br> | #### `volumes`<br><br>Definiert benannte Volumen, die von den Diensten verwendet werden. Diese können verwendet werden, um Daten zwischen Containerläufen persistent zu speichern.<br><br><br> |
| ```<br>networks:<br>  my_network:<br>    driver: bridge<br>```<br><br>  <br><br><br><br> | #### `networks`<br><br>Definiert Netzwerke, die von den Diensten verwendet werden. Netzwerke ermöglichen die Kommunikation zwischen Containern.<br><br><br> |

  
 ```
  

- **`version`**: Version des Compose File Formats.
- **`services`**: Containerdienste und ihre Konfigurationen.
    - **`image`**: Docker-Image des Dienstes.
    - **`build`**: Bauen des Images aus einem Dockerfile.
    - **`container_name`**: Name des Containers.
    - **`restart`**: Restart-Policy des Containers.
    - **`environment`**: Umgebungsvariablen.
    - **`volumes`**: Datenvolumes.
    - **`ports`**: Veröffentlichte Ports.
    - **`expose`**: Exponierte Ports.
    - **`networks`**: Netzwerke.
    - **`secrets`**: Docker Secrets.
- **`secrets`**: Definition von Secrets.
- **`volumes`**: Definition von Volumen.
- **`networks`**: Definition von Netzwerken.
- 

```
 ```
version: '3.9'

services:
  my_service:
    image: my_service_image:latest
    build: .
    container_name: my_service_container
    restart: always
    environment:
      - ENV_VAR1=value1
      - ENV_VAR2=value2
    volumes:
      - ./data:/data
    ports:
      - "8080:80"
    expose:
      - "8080"
    networks:
      - my_network
    secrets:
      - my_secret
secrets:
  my_secret:
    file: my_secret.txt
 

volumes:
  data_volume:
    driver: local

networks:
  my_network:
    driver: bridge
```

  

### ▪ Sie orchestrieren eine Multi-Container-Anwendung mit Hilfe von docker compose.

###   

### ▪ Sie containerisieren ein .NET-Projekt mit einem Dockerfile und docker-compose.yml

  

#### Dockerfile

  

```
# 1. Build compile image

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build-env
WORKDIR /build
COPY . .
RUN dotnet restore
RUN dotnet publish -c Release -o out

# 2. Build runtime image

FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY --from=build-env /build/out .
ENV ASPNETCORE_URLS=http://*:5001
EXPOSE 5001
ENTRYPOINT ["dotnet", "WebApi.dll"] ## projektname.dll
```

  

### Docker compose

  

```
version: '3.9'
services:
  webapi:
    build: WebApi ##<folder_name> here is the dockerfile
    restart: always
    ports:
      -  5001:5001 ##<host_port>:<container_port>
    environment:
      DatabaseSettings__ConnectionString: "mongodb://gbs:geheim@mongodb:27017"
      DatabaseSettings__Testx: "testx value from docker file"
      ##DatabaseSettings__Testy: "testy value from docker file"
    depends_on:
      - mongodb
```

  

### ▪ Sie finden Fehler in einem docker-compose.yml

###   

### 2\. Häufige Fehler und deren Lösungen

#### A. Einrückungsprobleme

Stellen Sie sicher, dass alle Ebenen korrekt eingerückt sind. YAML verwendet Leerzeichen zur Strukturierung, keine Tabs.

**Beispiel eines Einrückungsfehlers:**

**```
services:
  web:
  image: nginx
```

  

Korrekt:  
  

```
services:
  web:
    image: nginx
```

  

#### B. Unerwartete Schlüsselwörter

  

  

Falsch:

  

```
servics:
  web:
    image: nginx
```

  

Korrekt:  
  

```
services:
  web:
    image: nginx
```

  

#### C. Fehlende Doppelpunkte

  

  

Falsch:

  

```
services:
  web
    image nginx
```

  

Korrekt:

```
services:
  web:
    image: nginx
```

  

  

### D. Abhängigkeiten

  

```
services:
  web:
    depends_on:
      - non_existing_service
```

  

E.** Docker Secrets**  
Falsch:  

```
services:
  web:
    secrets:
      - non_existing_secret
```

  

Korrekt:  
  
  

```
services:
  web:
    secrets:
      - my_secret

secrets:
  my_secret:
    file: my_secret.txt
```

  

  

### Zusammenfassung

1. Verwenden Sie einen YAML-Syntax-Validator.
2. Verwenden Sie `docker-compose config`, um die Syntax zu überprüfen.
3. Achten Sie auf Einrückungen und Formatierungsfehler.
4. Überprüfen Sie die Konfiguration von Images und Builds.
5. Stellen Sie sicher, dass Umgebungsvariablen, Volumes, Ports, Netzwerke und Abhängigkeiten korrekt definiert sind.
6. Überprüfen Sie die Docker Secrets, wenn diese verwendet werden

  
  
  
**

  

### ▪ Sie richten in docker-compose.yml ein Netzwerk für die verschiedenen Services ein

###   

### ▪ Sie verwenden docker compose up / down und setzen die Optionen -d und --build ein

  

|     |     |
| --- | --- |
| <br> | <br> |
| ```<br>docker-compose up<br>```<br><br><br> | Der `docker-compose up` Befehl erstellt und startet Container, die in der `docker-compose.yml`<br> |
| ```<br>-d<br>```<br><br><br> | **`-d`**: Startet die Container im Hintergrund. Das Terminal ist danach wieder frei für weitere Befehle.<br> |
| ```<br>--build<br>```<br><br><br> | **`--build`**: Baut die Images neu, auch wenn sich die Dockerfiles nicht geändert haben.<br> |
| ```<br>docker-compose down<br>``` | Der `docker-compose down` Befehl stoppt und entfernt Container, Netzwerke, Volumes und Images, die von `docker-compose up` erstellt wurden.<br> |

  

Wichtig immer im gleichen verzeichniss sein wie das docker compose file sein

  

  

### Docker secrets:

  

### ▪ Sie verwenden Secrets für die Übergabe von Passwörtern

```
services:
   db:
     image: mysql:latest
     environment:
       MYSQL_ROOT_PASSWORD_FILE: /run/secrets/db_root_password ##pfad zum secret das das Password für den root user enthält 
       MYSQL_PASSWORD_FILE: /run/secrets/db_password
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
     secrets: ##liste der secrets die dem container zu verfügung gestellt werden 
       - db_root_password
       - db_password
     
secrets:
   db_password:
     file: db_password.txt ##path to the file 
   db_root_password:
     file: db_root_password.txt
```

  

## Microservices:

### ▪ Sie erklären den Begriff Microservice und argumentieren mit Vor- und Nachteilen

  

ie Microservice-Architektur ist ein Ansatz für das Systemdesign, bei dem komplexe Systeme in kleinere, besser verwaltbare Services aufgeteilt werden. Aus der Verwendung von Microservice-Frameworks ergeben sich skalierbarere, flexiblere und einfacher zu wartende Systeme.

  

Vorteile der Microservice-Architektur

- Schnellere Skalierbarkeit
- Verbesserte Fehlerisolierung
- Höhere Teamproduktivität
- Kürzere Deployment-Zeit
- Höhere Kosteneffizienz

  

### 1. Schnellere Skalierbarkeit

Da die einzelnen Services innerhalb der Microservice-Architektur unabhängig voneinander sind, können DevOps-Teams neue Komponenten nahtlos und ohne Ausfallzeit einführen. Sie können ohne Kompatibilitätsbedenken die beste Sprache oder Technologie für jeden Service auswählen.

Das Bereitstellen von Services auf mehreren Servern kann die Auswirkungen einzelner Komponenten auf die Leistung mindern und Unternehmen helfen, eine Bindung an einen bestimmten Anbieter zu vermeiden.

### 2. Verbesserte Fehlerisolierung

Die Microservice-Architektur besteht aus voneinander unabhängigen Teilen. Wenn bei einem Service ein Fehler oder ein Ausfall auftritt, breitet sich das Problem nicht auf das gesamte System aus.

### 3. Höhere Teamproduktivität

Die Microservice-Architektur ermöglicht es kleinen, spezialisierten Teams, sich auf die Entwicklung, das Deployment und die Wartung eines bestimmten Service zu konzentrieren, ohne durch die Komplexität des gesamten Systems belastet zu werden.

Die Microservice-Architektur fördert die Eigenverantwortung und das Fachwissen innerhalb der Teams, sodass spezialisierte Teammitglieder fundierte Entscheidungen treffen, schnell iterieren und eine hohe Servicequalität in ihrer Domain aufrechterhalten können.

### 4. Kürzere Deployment-Zeit

In monolithischen Architekturen erfordert jede Änderung ein erneutes Deployment der gesamten Anwendung. Die Microservice-Architektur dagegen ermöglicht schnellere Releases, da jeder Service eigenständig weiterentwickelt und bereitgestellt wird, wodurch das Risiko und der Zeitaufwand der Koordinierung von Änderungen in der gesamten Anwendung gemindert werden.

Diese Entkopplung der Services verbessert die Agilität. Du kannst Updates oder Fixes schnell und mit minimaler Unterbrechung des Gesamtsystems bereitstellen.

### 5. Höhere Kosteneffizienz

Die Microservice-Architektur optimiert die Ressourcenzuweisung und Wartung, da Teams an kleinen, klar definierten Services arbeiten. Der Aufwand wird auf bestimmte Services beschränkt, wodurch die Gesamtkosten für Entwicklung und Systemwartung sinken. Die Teams konzentrieren sich auf bestimmte Funktionen und stellen sicher, dass Ressourcen effizient und ohne Redundanz oder Überkapazitäten

  

  

- **Erhöhte Komplexität:** Da Microservices verteilt sind, kann das Management der Servicekommunikation eine Herausforderung sein. Entwickler müssen möglicherweise zusätzlichen Code schreiben, um eine reibungslose Kommunikation zwischen den Modulen zu gewährleisten.
- **Herausforderungen bei Deployment und Versionierung:** Die Koordination der Deployments und die Versionskontrolle über mehrere Services hinweg können komplex sein und zu Kompatibilitätsproblemen führen.
- **Komplexität bei Tests:** Das Testen von Microservices beinhaltet komplexe Szenarien, hauptsächlich bei Integrationstests für die verschiedenen Services. Die Orchestrierung kann dabei eine Herausforderung sein.
- **Schwierigkeiten bei der Fehlerbehebung:** Es kann schwierig sein, Fehler in einer Anwendung zu beheben, die mehrere Microservices mit jeweils eigenen Protokollen enthält. Ein einziger Geschäftsprozess kann auf mehreren Systemen gleichzeitig ausgeführt werden, was die Komplexität zusätzlich erhöht.
- **Herausforderungen beim Datenmanagement:** Die Datenkonsistenz und Transaktionen über mehrere Services hinweg können komplex sein. Die Microservice-Architektur erfordert eine sorgfältige Datenverwaltung und -koordination, um die Datenintegrität zu unterstützen.

Viele Unternehmen bevorzugen Microservice-Architekturen zunehmend. Du kannst betriebliche Hürden überwinden und die Widerstandsfähigkeit innerhalb eines auf Microservices basierenden Systems fördern, indem du [Best Practices für DevOps](https://www.atlassian.com/de/devops/what-is-devops/devops-best-practices) befolgst und die unten genannten Verfahren anwendest.

  

  

  

## Wordpress und mysql docker compose

  

```powershell
version: "3.9"
services:
  wordpress:
    image: wordpress
    depends_on:
      - db
    restart: always
    ports:
      - 80:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: gbs
      WORDPRESS_DB_PASSWORD_FILE: /run/secrets/db_password
      WORDPRESS_DB_NAME: gbsdb
    volumes:
      - html:/var/www/html
    secrets:
      - db_password
  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: gbsdb
      MYSQL_ROOT_PASSWORD_FILE: /run/secrets/root_password
      MYSQL_USER: gbs
      MYSQL_PASSWORD_FILE: /run/secrets/db_password
    volumes:
      - mysql:/var/lib/mysql
    secrets:
      - root_password
      - db_password
volumes:
  html:
  mysql:
secrets:
  root_password:
    file: ./secret1.txt
  db_password:
    file: ./secret2.txt
```
