# Les 6 - Docker

## Docker

Docker is niet zozeer Infrastructure as Code als de tools waar we de afgelopen weken naar gekeken hebben. Maar het heeft wel veel voordelen als je met Infrastructure as Code gaat werken of als je als organisatie richting een DevOps manier van werken wil.
Doordat een applicatie niet langer (handmatig) geinstalleerd wordt, met een rij aan handmatig op te lossen dependencies, zou je kunnen stellen dat het een soort van idempotency in zich heeft.

## De basis van containerisatie

Containerisatie heeft de softwareontwikkeling fundamenteel veranderd door applicaties en hun afhankelijkheden samen te verpakken in gestandaardiseerde, draagbare eenheden. Anders dan traditionele virtualisatie deelt een container het onderliggende besturingssysteem met de host, maar draait in een geïsoleerde omgeving met eigen bestandssysteem, CPU, geheugen, processen en netwerken. Dit zorgt voor consistentie tussen verschillende omgevingen, van ontwikkeling tot productie.

De waarde van containerisatie zit in het oplossen van het "het werkt op mijn machine" probleem. Ontwikkelaars kunnen hun applicatie en alle afhankelijkheden bundelen in één container, waardoor ze zeker weten dat deze overal identiek draait. Door hun lichtgewicht karakter kunnen containers binnen seconden worden opgestart en afgesloten, wat zeer efficiënte schaalbaarheid mogelijk maakt. De isolatie tussen containers voorkomt conflicten tussen applicaties, terwijl de portabiliteit ervoor zorgt dat applicaties op elk platform met een containerruntime kunnen draaien.

## Docker: De motor achter moderne containerisatie

Docker heeft containerisatie toegankelijk gemaakt voor een breed publiek sinds de lancering in 2013. De architectuur bestaat uit verschillende kerncomponenten die samen een krachtig ecosysteem vormen.

De Docker Engine fungeert als het hart van het systeem. Deze runtime bestaat uit een daemon (containerd), een gebruiksvriendelijke command-line interface, en een REST API voor communicatie tussen componenten. Docker Images zijn de blauwdrukken waaruit containers worden gemaakt. Deze bevatten alle code, bibliotheken, en systeemtools die nodig zijn om een applicatie te draaien.

## Dockerfiles: De bouwplannen voor containers

Een Dockerfile is een tekstbestand met instructies die Docker vertellen hoe een image gebouwd moet worden. Het fungeert als een soort recept voor het creëren van een Docker-image, waarin elke regel een nieuwe laag in het image vertegenwoordigt. Deze gelaagde aanpak maakt Docker images efficiënt en makkelijk te onderhouden.

### Anatomie van een Dockerfile

Een Dockerfile bestaat uit een reeks instructies die Docker stap voor stap uitvoert. Elke instructie begint met een sleutelwoord (zoals FROM, COPY, RUN) gevolgd door argumenten. Hier zijn de meest gebruikte instructies:

**FROM**: Specificeert de basis-image waarop gebouwd wordt. Dit is altijd de eerste instructie in een Dockerfile. Bijvoorbeeld: `FROM ubuntu:24.04` of `FROM node:22`. Het volgt meestal het formaat `[image]:[tag]`.

**WORKDIR**: Stelt de werkdirectory in voor alle volgende instructies. Dit is waar uw applicatie binnen de container zal leven. Bijvoorbeeld: `WORKDIR /app`.

**COPY** en **ADD**: Kopiëren bestanden van de lokale bouwcontext naar het image. COPY is eenvoudiger en kopieert alleen lokale bestanden, terwijl ADD extra functionaliteiten heeft zoals het uitpakken van archieven. Bijvoorbeeld: `COPY . .` kopieert alle bestanden uit de huidige directory naar de werkdirectory in het image.

**RUN**: Voert commando's uit tijdens het bouwen van het image en slaat het resultaat op als een nieuwe laag. Vaak gebruikt voor het installeren van afhankelijkheden. Bijvoorbeeld: `RUN apt-get update && apt-get install -y python3`.

**ENV**: Stelt omgevingsvariabelen in die beschikbaar zijn tijdens het bouwen en in de draaiende container. Bijvoorbeeld: `ENV NODE_ENV production`.

**EXPOSE**: Documenteert welke poorten de container gebruikt tijdens het draaien. Dit is puur informatief; om daadwerkelijk poorten te openen moet je parameters meegeven bij het starten van de container. Bijvoorbeeld: `EXPOSE 80`.

**CMD** en **ENTRYPOINT**: Definieert wat er gebeurt wanneer de container start. CMD geeft het standaard commando en argumenten, terwijl ENTRYPOINT het uitvoerbare bestand specificeert. Er kan slechts één CMD-instructie in een Dockerfile zijn. Bijvoorbeeld: `CMD ["node", "server.js"]`.

**VOLUME**: Creëert een mountpunt voor persistent data dat de levensduur van de container overstijgt. Bijvoorbeeld: `VOLUME /data`.

### De bouwcontext en lagen begrijpen

Wanneer je een Docker image bouwt met `docker build .`, verwijst de punt naar de "bouwcontext" – de directory die alle bestanden bevat die naar Docker worden gestuurd voor het bouwproces. Docker bouwt elke instructie als een aparte laag, wat een aantal belangrijke implicaties heeft:

1. **Caching**: Docker kan lagen cachen die niet zijn veranderd sinds de laatste build, wat het bouwproces aanzienlijk versnelt.

2. **Gelaagdheid**: Elke laag is afhankelijk van de lagen eronder, en bevat alleen de wijzigingen ten opzichte van de vorige laag.

3. **Optimalisatie**: De volgorde van instructies beïnvloedt de efficiëntie van caching. Instructies die vaak veranderen (zoals het kopiëren van uw applicatiecode) moeten later in het Dockerfile komen dan stabielere instructies (zoals het installeren van systeemdependencies).

### Best practices voor Dockerfiles

1. **Gebruik specifieke tags**: Vermijd `FROM node:latest` en gebruik in plaats daarvan specifieke versies zoals `FROM node:14.17.5` om reproduceerbaarheid te garanderen.

2. **Combineer gerelateerde RUN-instructies**: Gebruik de shell-operator `&&` om gerelateerde commando's te combineren, en `\` voor leesbaarheid bij meerdere regels. Dit vermindert het aantal lagen.

3. **Verwijder overbodige bestanden**: Ruim na elke installatie op om de imagegrootte te minimaliseren.

4. **Gebruik .dockerignore**: Creëer een .dockerignore-bestand om onnodige bestanden uit te sluiten van de bouwcontext.

5. **Gebruik multi-stage builds**: Voor complexe applicaties, gebruik meerdere FROM-instructies om build-tools en runtime-afhankelijkheden te scheiden.

6. **Draai als niet-root gebruiker**: Voeg een `USER`-instructie toe om de container als een niet-root gebruiker te draaien, wat veiliger is.

Nu we een goed begrip hebben van Dockerfiles, laten we een voorbeeld bekijken van een Node.js applicatie:

```dockerfile
# Basisimage: Node.js versie 14 op Alpine Linux (klein besturingssysteem)
FROM node:22-alpine

# Stel werkdirectory in
WORKDIR /app

# Kopieer package.json en package-lock.json eerst om gebruik te maken van Docker's cache
COPY package*.json ./

# Installeer afhankelijkheden
RUN npm install --production

# Kopieer de applicatiecode (dit verandert vaak, dus komt later in het Dockerfile)
COPY . .

# Stel omgevingsvariabele in
ENV NODE_ENV=production

# Poort die de applicatie gebruikt
EXPOSE 3000

# Definieer een niet-root gebruiker voor beveiliging
USER node

# Commando om uit te voeren bij container start
CMD ["node", "app.js"]
```

Dit Dockerfile volgt best practices door:

- Een specifieke Node.js versie te gebruiken
- Gebruik te maken van Docker's caching-mechanisme door package.json eerst te kopiëren
- Alleen productie-afhankelijkheden te installeren
- De applicatie als een niet-root gebruiker te draaien
- Commando's logisch te ordenen van minst-vaak-veranderend naar meest-vaak-veranderend

Docker Registry biedt een centrale opslagplaats voor images. Docker Hub dient als de officiële publieke registry, terwijl veel organisaties private registries gebruiken voor gevoelige of bedrijfseigen images.

## Docker in actie: Een blik op draaiende containers

Wanneer Docker-containers draaien op een host, kun je deze bekijken met het commando `docker ps`. Dit geeft een overzicht van alle actieve containers, hun status en configuratie. Hieronder zie je hoe dit er in de praktijk uit zou kunnen zien:

```
CONTAINER ID   IMAGE                  COMMAND                  CREATED          STATUS          PORTS                                       NAMES
a72f6e346a32   nginx:latest           "/docker-entrypoint.…"   2 minutes ago    Up 2 minutes    0.0.0.0:80->80/tcp, :::80->80/tcp           web-server
f836d9a7c1d4   postgres:13            "docker-entrypoint.s…"   15 minutes ago   Up 15 minutes   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   database
c45e7c986a2d   redis:6                "docker-entrypoint.s…"   23 minutes ago   Up 23 minutes   6379/tcp                                    cache
814f6d8750fe   company/api:v2.3       "node server.js"         3 hours ago      Up 3 hours      0.0.0.0:3000->3000/tcp                      api-service
9b2fc8b65321   company/worker:latest  "python worker.py"       2 days ago       Up 2 days                                                   background-worker
e67d3c932811   grafana/grafana        "/run.sh"                5 days ago       Up 5 days       0.0.0.0:3001->3000/tcp                      monitoring
```

Dit overzicht toont diverse containers die verschillende diensten draaien, zoals een webserver (NGINX), database (PostgreSQL), caching-service (Redis), een API-service, een achtergrondverwerker en een monitoring-tool (Grafana). Elke container heeft een unieke ID, is gebaseerd op een specifieke image, draait een bepaald commando, en kan poorten van de host doorverwijzen naar poorten binnen de container (zoals 0.0.0.0:80->80/tcp voor de webserver).

Deze containers draaien allemaal geïsoleerd van elkaar, maar kunnen via het Docker-netwerk met elkaar communiceren. Ze delen het besturingssysteem van de host, maar hebben elk hun eigen processen, bestandssystemen en netwerkstacks.

## Docker Compose: Orkestratie van complexe applicaties

Docker Compose maakt het mogelijk om meerdere containers als één samenhangend systeem te beheren. Door een declaratieve YAML-configuratie te gebruiken, kan een ontwikkelaar een volledige applicatie met al haar componenten definiëren. Docker Compose automatiseert vervolgens het creëren van netwerken, volumes, en het starten van containers in de juiste volgorde.

Stel je voor dat je een webapplicatie ontwikkelt met een frontend, backend API, en een database. Met Docker Compose definieert u deze structuur in één bestand:

```yaml
version: "3"
services:
  frontend:
    build: ./frontend
    ports:
      - "80:80"
    depends_on:
      - api

  api:
    build: ./api
    environment:
      DATABASE_URL: postgres://postgres:example@db:5432/app
    depends_on:
      - db

  db:
    image: postgres:13
    environment:
      POSTGRES_PASSWORD: example
      POSTGRES_DB: app
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

Met één enkel commando (`docker-compose up`) wordt deze volledige stack opgestart, met alle juiste netwerkverbindingen en volumekoppelingen. Dit vereenvoudigt het ontwikkelproces aanzienlijk, omdat ontwikkelaars niet langer complexe Docker-commando's hoeven te onthouden of scripts hoeven te schrijven voor het opstarten van hun ontwikkelomgeving.

Docker Compose is bijzonder waardevol voor het opzetten van lokale ontwikkelomgevingen, geautomatiseerde testomgevingen, demonstratie-opstellingen, en staging-omgevingen. Het helpt ontwikkelaars om zich te concentreren op hun code, terwijl de infrastructuur wordt gestandaardiseerd en reproduceerbaar wordt gemaakt.

## Beheer van Docker-containers met Ansible

Terwijl Docker Compose uitstekend werkt voor lokale ontwikkeling en kleinschalige implementaties, vereist het beheren van containers in grotere productieomgevingen meer geavanceerde automatiseringstools. Hier komt Ansible in beeld – een krachtig IT-automatiseringsplatform dat het beheer van Docker-containers naar een hoger niveau tilt.

Ansible is een configuratiebeheertool die werkt vanuit het principe van idempotentie: ongeacht hoe vaak u een taak uitvoert, het resultaat blijft hetzelfde. Voor Docker-beheer betekent dit dat Ansible kan controleren of containers draaien met de juiste configuratie, en alleen wijzigingen aanbrengt waar nodig.

Het gebruik van Ansible voor Docker-beheer biedt verschillende voordelen. Ten eerste kan Ansible zowel de Docker-infrastructuur als de containers zelf beheren. Dit betekent dat het niet alleen containers kan starten en stoppen, maar ook de onderliggende Docker Engine kan installeren, configureren en updaten op tientallen of honderden servers tegelijk.

Een typisch Ansible-playbook voor Docker-beheer zou er als volgt uit kunnen zien:

```yaml
---
- name: Configureer Docker op webservers
  hosts: webservers
  become: yes
  tasks:
    - name: Installeer Docker
      apt:
        name: docker.io
        state: present
        update_cache: yes

    - name: Start en enable Docker service
      service:
        name: docker
        state: started
        enabled: yes

    - name: Pull de nieuwste versie van de webapp image
      docker_image:
        name: company/webapp
        source: pull
        force_source: yes

    - name: Start webapp containers
      docker_container:
        name: "webapp-{{ inventory_hostname }}"
        image: company/webapp:latest
        state: started
        restart_policy: always
        ports:
          - "80:80"
        env:
          NODE_ENV: production
```

Dit playbook automatiseert de installatie van Docker, het downloaden van images, en het starten van containers op alle servers in de "webservers" groep. Het grote voordeel van deze aanpak is schaalbaarheid en consistentie. Dezelfde configuratie wordt toegepast op elke server, waardoor configuratiedrift wordt voorkomen.

Ansible ondersteunt ook meer geavanceerde Docker-orchestratiescenario's. Bijvoorbeeld, het kan integreren met Docker Swarm of Kubernetes voor het beheren van containerorkestratieclusters, het uitvoeren van rolling updates zonder downtime, en het implementeren van complexe netwerkconfiguraties.

Voor organisaties die een CI/CD-pijplijn implementeren, kan Ansible dienen als de deployment-laag. Wanneer een nieuwe versie van een applicatie wordt gebouwd en getest, kan Ansible automatisch de nieuwe container-images uitrollen naar de productieomgeving, met ingebouwde controlemechanismen om de gezondheid van de implementatie te verifiëren.

Een ander krachtig gebruik van Ansible met Docker is het beheren van de gehele container-levenscyclus, inclusief gezondheidscontroles, backups, en monitoring. Met Ansible's integratie met monitoring-tools zoals Prometheus kan het automatisch reageren op problemen door containers te herstarten of te schalen wanneer nodig.

## Docker in de praktijk

In de praktijk wordt Docker ingezet in een breed scala aan scenario's. Ontwikkelteams gebruiken Docker om identieke ontwikkelomgevingen te garanderen voor alle teamleden, ongeacht hun lokale besturingssysteem. Grote organisaties zoals Netflix, Uber en PayPal implementeren microservices-architecturen met Docker, waarbij elke service in haar eigen container draait.

In CI/CD-pijplijnen maken Docker-containers het mogelijk om code consistent te testen en implementeren, terwijl in de data science wereld Docker zorgt voor reproduceerbare onderzoeksomgevingen met complexe afhankelijkheden. Legacy-applicaties kunnen worden gecontaineriseerd om ze portabel te maken zonder herschrijven, wat migratie naar de cloud aanzienlijk vereenvoudigt.

De combinatie van Docker voor containerisatie, Docker Compose voor lokale ontwikkeling en testen, en Ansible voor grootschalig beheer, vormt een krachtige toolset voor moderne software-implementatie. Deze technologieën samen maken het mogelijk om applicaties betrouwbaar, schaalbaar en consistent uit te rollen, van de laptop van een ontwikkelaar tot grote productieclusters met duizenden servers.

> Opdracht :
> Maak een complete deployment waarin je een Azure VM en ESXi VM combineert en je een hybrid cloud situatie maakt. Gebruik de stof van de afgelopen lessen. De deployment is compleet geautomatiseert, inclusief het aanmaken van VM's en andere resources in Azure. Je maakt op beide omgeving een gebruiker 'testuser' aan, via Ansible of via Terraform. De testuser kan inloggen van de ESXI VM naar de Azure VM, het plaatsen van de benodigde SSH keys is geautomatiseerd. Op beide systemen draait een "Hello World" Docker container en je gebruikt een role om docker te installeren. De omgeving kan automatisch via CI/CD uitgerold worden (dit hoeft alleen voor ESXi, want je hebt niet genoeg rechten hiervoor op Azure).

Voor week 6 hoef je niet specifiek iets in te leveren. Dit kun je bij je uiteindelijke eindoplevering plaatsen.

```

```
