#### LAB

> Opdracht 1 — Repository & eerste VM (branch: test)
> Maak een nieuwe repository les-03.
> - Voeg een juiste .gitignore, README.md en eventueel LICENSE toe.
> - Documenteer in de README hoe je de VM kunt deployen en hoe je Ansible gebruikt.
> - Maak een nieuwe branch test.
>
> Deliverables:
> - link naar repository met gevraagde bestanden
>
> Terraform:
> Schrijf een manifest dat één Ubuntu VM deployt.
> Je moet met een SSH-key kunnen inloggen.
> Terraform genereert automatisch een inventory.
> In deze inventory staat bij de host een variabele app_name="demoapp".
>
> Opdracht 2 — Variabelen gebruiken in een playbook (branch: test)
> Definieer in het playbook een variabele motd_message: "Welkom bij het lab" en zet deze tekst in /etc/motd.
> Gebruik de variabele app_name uit de inventory om een bestand /etc/app.conf te maken met daarin:
> Application = demoapp
> Voer het command uname -r uit en registreer de output in een variabele kernel_version.
> Schrijf de waarde van kernel_version.stdout naar /tmp/kernel.txt.
> Gebruik facts om de distributienaam (ansible_distribution) in een bestand /tmp/facts.txt te zetten.
> Voer het playbook 2x uit.
>
> Deliverables:
> - link naar repository met gevraagde bestanden
> - screenshot van de inhoud van het bestand /etc/motd en /tmp/facts.txt
>
> Opdracht 3 — Voorwaardelijke taken (branch: test)
> Schrijf een taak die alleen wordt uitgevoerd als de distributie Ubuntu is.
> Schrijf een taak die alleen wordt uitgevoerd als de distributie Red Hat is.
> Schrijf een taak die controleert of /etc/hosts bestaat.
> De taak faalt als het bestand niet bestaat (failed_when).
> Schrijf een taak die echo hallo uitvoert.
> Zorg dat deze taak niet als changed wordt weergegeven.
>
> Deliverables:
> - link naar repository met gevraagde bestanden
> - screenshot van de output.
>
> Opdracht 4 — Includes en Imports (feature branch: tasks)
> Maak een nieuwe feature branch tasks.
> Schrijf een playbook site.yaml dat taken inlaadt met import_tasks en include_tasks waarbij je een verschil tussen beide laat zien:
> Maak een extra role role_firewall die UFW installeert en poort 8080 openzet.
> Voeg deze role toe in site.yaml.
> Beschrijf in je README:
> Het verschil tussen import_tasks en include_tasks en in welke gevallen je ze zou gebruiken
> Waarom het handig is om roles te gebruiken.
>
> Deliverables:
> - screenshot van test of poort 8080 open is.
> - screenshot van de README
>
> Opdracht 4 — Productieomgeving met roles vanuit Ansible Galaxy (branch: productie)
> Maak een nieuwe branch productie.
> 
> Deploy drie VM’s vanuit één manifest:
> - Twee webservers (web-1, web-2).
> - Eén database server (db-1).
>
> Terraform genereert automatisch een inventory met de groepen web en db.
> Gebruik host_vars en group_vars
> In group_vars/web/vars.yml zet je:
> - nginx_port: 8080
> 
> In host_vars/db-1/vars.yml zet je:
> - db_name: myapp
>
> Roles
> Maak lokaal de role role_web die nginx installeert en configureert.
> Nginx moet draaien op poort 8080 (gebruik de variabele nginx_port).
> Gebruik een Jinja2-template index.html.j2 die de hostname en datum toont.
>
> Maak de role role_db die mariadb-server installeert en upload deze naar Ansible Galaxy.
> In de configuratie moet de database naam myapp gebruikt worden (variabele db_name).
> Schrijf een playbook prod.yaml dat:
> De groep web de role role_web geeft.
> De groep db de role role_db (vanuit Ansible Galaxy) geeft.
>
> Deliverables:
> - screenshot van jouw role in Ansible Galaxy.

Zorg dat je code in versiebeheer staat en lever van alle opdrachten een screenshot in op Brightspace.