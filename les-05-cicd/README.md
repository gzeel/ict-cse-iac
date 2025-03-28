# Week 5 - CI/CD

## Ansible (3) Code testen

Wanneer je bezig bent een applicatie te ontwikkelen is het heel normaal dat je de software code test voordat je de applicatie in een productiomgeving beschikbaar maakt. We hebben de afgelopen weken besteed om onze infrastructuur ook als code te gaan zien. Waarom zouden we deze code dan niet testen?

We kunnen dezelfde manieren en methodes van software testing ook toepassen op infrastructuurcode. Denk dus aan unit, functionele, integratie en acceptatietesten.
Unit-testen, toegepast op applicaties, zijn het testen van de kleinste code-eenheden (meestal functies of klassenmethoden). In Ansible zouden unit-tests doorgaans van toepassing zijn op individuele draaiboeken. Je zou individuele draaiboeken in een geïsoleerde omgeving kunnen draaien, maar dat is vaak de moeite niet waard. Wat wel de moeite waard is, is controleren op playbook-syntax, om er zeker van te zijn dat je playbook faalt vanwege een ontbrekend aanhalingsteken of een tab of spatie issue! Dit wordt ook wel linting genoemd.
Linting kun je ook al inschakelen in je IDE zoals VSCode. Er zijn specifieke Ansible, Terraform etc linting extensies beschikbaar.

In een functionele test test je bijvoorbeeld de werking en output van een enkel playbook of role. Doet deze wat het zou moeten doen?
Je kunt dit bijvoorbeeld doen door het playbook te draaien en de output te bekijken.

Integratietesten zijn bijvoorbeeld van toepassing voor Ansible bij het gebruik van rollen. Werken je playbooks nog goed wanneer je ze in grotere eenheden integreert? Denk aan het testen van rollen. Je wil dat de rollen in meerdere playbooks blijven werken en de verwachte output geven. Of heb je ergens in een playbook iets gezet wat de werking van je role ongedaan maakt o.i.d.?

Elke techniek die hieronder wordt besproken, biedt meer waarde dan de vorige, maar is ook complexer en misschien niet de extra installatie- en onderhoudslasten waard, afhankelijk van je playbook.

We beginnen met de eenvoudigste en meest basale tests en gaan dan over naar volwaardige functionele testmethoden en testautomatisering.

### Debugging en Asserting

Voor de meeste playbooks is het testen van configuratiewijzigingen en het resultaat van de uitgevoerde opdrachten het enige dat je nodig hebt. En als je tests met behulp van enkele ingebouwde hulpprogrammamodules van Ansible uitvoert tijdens het draaien van je playbook, weet je meteen zeker dat het systeem zich in de juiste staat bevindt.

Indien mogelijk moet je proberen alle eenvoudige testgevallen (bijvoorbeeld vergelijkingen en state controles) in je draaiboeken te verwerken. Ansible heeft drie modules die dit proces vereenvoudigen.

Als je een Ansible-playbook aan het ontwikkelen bent is het vaak handig om waarden van variabelen of de uitvoer van bepaalde opdrachten af te drukken tijdens de run van het playbook. Voor dit doel heeft Ansible een debug-module.

Als extreem eenvoudig voorbeeld zijn hier twee manieren waarop je debug kunt gebruiken tijdens het bouwen van een playbook:

```yaml
 1 ---
 2 - hosts: 127.0.0.1
 3   gather_facts: no
 4   connection: local
 5
 6   tasks:
 7     - name: Register the output of the 'uptime' command.
 8       command: uptime
 9       register: system_uptime
10
11     - name: Print the registered output of the 'uptime' command.
12       debug:
13         var: system_uptime.stdout
14
15     - name: Print a message if a command resulted in a change.
16       debug:
17         msg: "Command resulted in a change!"
18       when: system_uptime is changed'
```

Als je dit playbook zou uitvoeren krijg je de volgende uitvoer:

```bash
$ ansible-playbook debug.yml

PLAY [127.0.0.1] ****************************************************

TASK: [Register the output of the 'uptime' command.] ****************
changed: [127.0.0.1]

TASK [Print the registered output of the 'uptime' command.] *********
ok: [127.0.0.1] => {
    "system_uptime.stdout":
      "20:55  up  7:33, 4 users, load averages: 0.95 1.36 1.43'
      '}

TASK [Print a message if a command resulted in a change.] ***********
ok: [127.0.0.1] => {
    "msg": "Command resulted in a change!"
}

PLAY RECAP **********************************************************
127.0.0.1            : ok=3    changed=1    unreachable=0    failed=0'
```

Debug-berichten zijn nuttig bij het actief debuggen van een playbook of wanneer je extra verbosity nodig hebt in de uitvoer van het playbook, maar als je een expliciete test op een bepaalde variabele moet uitvoeren, of om een of andere reden een playbook moet verlaten, biedt Ansible de fail-module, en zijn meer beknopte variant, assert.

### De fail en assert module

Zowel fail als assert zullen, wanneer ze worden geactiveerd, de run van het playbook afbreken. Het belangrijkste verschil zit in de eenvoud van hun gebruik en wat er wordt uitgevoerd tijdens een run van het playbook. Voorbeeld :

```yaml
 1 ---
 2 - hosts: 127.0.0.1
 3   gather_facts: no
 4   connection: local
 5
 6   vars:
 7     should_fail_via_fail: true
 8     should_fail_via_assert: false
 9     should_fail_via_complex_assert: false
10
11   tasks:
12     - name: Fail if conditions warrant a failure.
13       fail:
14         msg: "There was an epic failure."
15       when: should_fail_via_fail
16
17     - name: Stop playbook if an assertion isn't validated.
18       assert:
19         that: "should_fail_via_assert != true"
20
21     - name: Assertions can have contain conditions.
22       assert:
23         that:
24           - should_fail_via_fail != true
25           - should_fail_via_assert != true
26           - should_fail_via_complex_assert != true'
```

Verander in bovenstaand voorbeeld de booleans van Should_fail_via_fail, Should_fail_via_assert en Should_fail_via_complex_assert om elk van de drie fail/assert-taken te activeren, en zie welk effect het heeft.

Een fail taak wordt gerapporteerd als overgeslagen als er geen fout wordt geactiveerd, terwijl een assert-taak die slaagt, wordt weergegeven als een ok-taak met een inline-bericht in de standaarduitvoer van Ansible:

```bash
TASK [Assertions can have contain conditions.] ********************
ok: [default] => {
    "changed": false,
    "msg": "All assertions passed"
}
```

Voor de meeste testgevallen zijn debug, fails en asserts alles wat je nodig hebt om ervoor te zorgen dat je infrastructuur in de desired state verkeert tijdens een playbook run.

### YAML en Ansible linting

Als je eenmaal een playbook hebt geschreven, is het een goed idee om ervoor te zorgen dat de basis-YAML-syntaxis correct is. Veel van de meest voorkomende fouten in Ansible-playbooks, vooral voor beginners, zijn whitespace problemen, de bekende inspringende spaties.
Je kunt in je VSCode IDE een yaml lint extensie aanzetten. Daarnaast kun je op linux/macos systemen de applicatie yamllint installeren.

Stel je zou een playbook hebben met de een fout erin:

```yaml
 1 - hosts: localhost
 2   gather_facts: no
 3   connection: local
 4
 5   tasks:
 6     - name: Register the output of the 'uptime' command.
 7       command: uptime
 8       register: system_uptime # comment
 9
10     - name: Print the registered output of the 'uptime' command.
11       debug:
12        var: system_uptime.stdout
```

En je laat de yamllint applicatie dit playbook nakijken, door yamllint in een directory aan te roepen, dan krijg je de volgende uitvoer:

```bash
$ yamllint .
./lint-example.yml
  1:1  warning missing document start "---"  (document-start)
  2:17 warning truthy value should be one of [false, true]  (truthy)
  7:22 error   trailing spaces  (trailing-spaces)
  8:31 warning too few spaces before comment  (comments)
  12:8 error   wrong indentation: expected 8 but found 7 (indentation)'
```

Hoewel het in eerste instantie misschien muggenzifterig lijkt, besef je na verloop van tijd hoe belangrijk het is om een specifieke stijl van coderen te gebruiken en daaraan vast te houden. Het ziet er beter uit en kan helpen voorkomen dat er fouten binnensluipen als gevolg van inspringingen, whitespaces of structurele problemen.

In dit specifieke geval kun je een aantal fouten snel herstellen:

- Voeg een yaml-documentstartindicator (---) toe bovenaan het draaiboek.
- Verwijder de extra ruimte op de opdrachtregel.
- Voeg een extra spatie toe vóór de # opmerking.
- Zorg ervoor dat de var-regel nog een spatie ingesprongen is.

Maar hoe zit het met de waarschuwing 'thruthy value'? In veel Ansible-voorbeelden wordt yes of no gebruikt in plaats van true en false. We kunnen dat toestaan door yamllint aan te passen met een configuratiebestand.

Maak een bestand in dezelfde map met de naam .yamllint, met de volgende inhoud:

```yaml
 1 ---
 2 extends: default
 3
 4 rules:
 5   truthy:
 6     allowed-values:
 7       - 'true'
 8       - 'false'
 9       - 'yes'
10       - 'no'
```

### --syntax-check

Het controleren van de syntax door Ansible is erg eenvoudig en vereist slechts een paar seconden.

Wanneer je een playbook uitvoert met --syntax-check, worden de plays niet uitgevoerd; in plaats daarvan laadt Ansible het hele playbook statisch en zorgt ervoor dat alles kan worden geladen zonder fatale fouten. Als je een geïmporteerd taakbestand mist, een modulenaam verkeerd hebt gespeld of een module met ongeldige parameters opgeeft, zal --syntax-check het probleem snel identificeren.

Je kunt --syntax-check erg goed gebruiken als je gebruik maakt van een CI omgeving (komen we later op) of als je een pre-commit test wil doen voordat je iets in Git versiebeheer zet.

Omdat syntaxiscontrole een playbook alleen statisch laadt, kunnen dynamische includes (zoals geladen met include_tasks) en variabelen niet worden gevalideerd. Hierdoor zijn er meer integratietesten nodig om te garanderen dat een heel playbook kan draaien.

## Gitlab CI/CD Pipeline

In de moderne softwareontwikkeling is snelheid van levering gecombineerd met kwaliteit van cruciaal belang. Organisaties moeten snel kunnen reageren op veranderende marktomstandigheden en klantwensen, terwijl ze tegelijkertijd betrouwbare en veilige software blijven leveren. CI/CD (Continuous Integration/Continuous Delivery) vormt de ruggengraat van deze aanpak en is een essentieel onderdeel geworden van de DevOps-cultuur.

### Wat is CI/CD?

#### Continuous Integration (CI)

Continuous Integration is een development werkwijze waarbij ontwikkelaars regelmatig hun codewijzigingen in een centrale repository samenvoegen, waarna geautomatiseerde builds en tests worden uitgevoerd. Het hoofddoel van CI is om integratiefouten vroeg in de ontwikkelingscyclus te identificeren en op te lossen.
In een effectieve CI-omgeving committen ontwikkelaars hun code meerdere keren per dag naar een gedeelde repository. Bij elke commit wordt een geautomatiseerd proces gestart dat de code bouwt en test. Dit proces zorgt ervoor dat nieuwe code compatibel is met de bestaande codebase en dat er geen regressies worden geïntroduceerd. Door deze frequente integratie worden problemen snel zichtbaar, waardoor ze gemakkelijker en goedkoper op te lossen zijn dan wanneer ze pas later in het ontwikkelingsproces worden ontdekt.
Een belangrijk aspect van CI is de snelle feedback die het ontwikkelaars geeft. Wanneer een build of test mislukt, worden ontwikkelaars onmiddellijk op de hoogte gesteld, zodat ze het probleem kunnen oplossen voordat ze verder gaan met nieuwe functionaliteit. Deze snelle feedbacklus bevordert een cultuur van kwaliteit en verantwoordelijkheid binnen het ontwikkelteam.
Naast het uitvoeren van unit tests kan een CI-pipeline ook statische code-analyse uitvoeren om codestandaarden te handhaven, beveiligingsproblemen te identificeren en technische schuld te voorkomen. Deze uitgebreide aanpak zorgt ervoor dat de code niet alleen functioneel correct is, maar ook voldoet aan de kwaliteitsnormen van de organisatie.

#### Continuous Delivery (CD)

Continuous Delivery bouwt voort op CI door ervoor te zorgen dat code niet alleen wordt geïntegreerd en getest, maar ook continu klaar is voor productie. Bij CD is het doel om de software in een staat te houden waarin deze op elk moment veilig naar productie kan worden gebracht.
In een CD-workflow wordt code die succesvol door de CI-fase is gekomen, automatisch gedeployed naar een testomgeving die de productieomgeving zo goed mogelijk nabootst. Hier ondergaat de software meer uitgebreide tests, waaronder functionele tests, prestatietests en beveiligingstests. Deze tests simuleren het echte gebruikersgedrag en valideren dat de software aan de bedrijfsvereisten voldoet.
Een belangrijk principe van CD is dat elke codewijziging potentieel naar productie kan gaan. Dit betekent dat het releaseproces volledig geautomatiseerd moet zijn, met uitzondering van de uiteindelijke beslissing om de code naar productie te promoten, die vaak handmatig blijft. Deze aanpak vermindert de risico's die gepaard gaan met releases door het proces voorspelbaar, herhaalbaar en betrouwbaar te maken.
CD vereist ook een robuuste infrastructuur voor configuratiebeheer en omgevingsbeheer. Door gebruik te maken van Infrastructure as Code (IaC) kunnen teams consistente omgevingen creëren en beheren, waardoor het risico op omgevingsgerelateerde problemen tijdens de deployment wordt geminimaliseerd.

#### Continuous Deployment (CD)

Continuous Deployment gaat nog een stap verder dan Continuous Delivery. Terwijl bij Continuous Delivery de beslissing om naar productie te gaan handmatig blijft, wordt bij Continuous Deployment elke wijziging die alle geautomatiseerde tests doorstaat, automatisch naar productie gepusht zonder menselijke tussenkomst.
Deze aanpak vereist een hoog niveau van vertrouwen in de automatiseringsprocessen en testsuites. Organisaties die Continuous Deployment toepassen, hebben doorgaans uitgebreide monitoring- en alertingsystemen om snel problemen in productie te detecteren en te reageren. Ze maken ook vaak gebruik van technieken zoals feature flags en canary releases om risico's te beheersen en de impact van nieuwe functionaliteit geleidelijk te evalueren.
Continuous Deployment is niet voor elke organisatie of elk product geschikt. Regelgevende vereisten, klantvoorkeuren of de aard van het product kunnen een volledig geautomatiseerd releaseproces belemmeren. In dergelijke gevallen kan Continuous Delivery een passender aanpak zijn, waarbij de software altijd klaar is voor release, maar de daadwerkelijke deployment naar productie een bewuste beslissing blijft.

#### GitHub Actions

GitHub Actions is een krachtige CI/CD-oplossing die direct geïntegreerd is in GitHub. Je hoeft geen aparte runners te installeren of te configureren, omdat GitHub al runners host die je workflows kunnen uitvoeren.
Om aan de slag te gaan met GitHub Actions, moet je een workflow bestand aanmaken in je repository. Dit doe je door een directory genaamd .github/workflows aan te maken en daarin een YAML-bestand te plaatsen dat je workflow definieert. In dit bestand specificeer je wanneer de workflow moet worden uitgevoerd, zoals bij een push naar een bepaalde branch of bij het aanmaken van een pull request, en welke acties moeten worden ondernomen.

GitHub Actions maakt gebruik van een event-gebaseerd systeem. Wanneer een bepaalde gebeurtenis plaatsvindt in je repository, zoals een push of pull request, kan dit een workflow triggeren. In je workflow bestand definieer je jobs die op verschillende runners kunnen worden uitgevoerd. Deze jobs bestaan uit een reeks stappen die sequentieel worden uitgevoerd.

Hier is een voorbeeld van een workflow bestand:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: "22"
      - name: Install dependencies
        run: npm ci
      - name: Run tests
        run: npm test
```

Je ziet hier een `build` job met een aantal acties. De workflow gaat lopen als er een push of een pull-request wordt uitgevoerd.

Een van de krachtigste aspecten van GitHub Actions is het uitgebreide ecosysteem van herbruikbare acties. Deze vooraf gebouwde componenten kunnen worden gebruikt om veelvoorkomende taken uit te voeren, zoals het uitchecken van code, het opzetten van een programmeeromgeving, of het deployen naar een cloudprovider. Dit bespaart tijd en vermindert de hoeveelheid code die je zelf moet schrijven.

Nadat je je workflow bestand hebt gecommit en gepusht naar GitHub, kun je de uitvoering ervan volgen via de Actions tab in je repository. Hier zie je een overzicht van alle workflow runs, hun status, en gedetailleerde logs die helpen bij het debuggen van eventuele problemen.
