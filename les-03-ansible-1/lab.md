#### LAB

> Opdracht 1
>
> - Maak een nieuwe repository les-03 aan.
> - Maak een nieuwe git branch `test` aan en werk voor opdracht 1 en 2 in deze branch.
> - Deploy met Terraform een VM en zorg dat je in kunt loggen op deze vm dmv een ssh-key. Zorg dat er bij het deployen van deze VM automatisch een inventory wordt aangemaakt.

> Opracht 2
>
> - In de `test` branch:
> - Maak playbooks voor de volgende taken:
> - Alle packages op een Ubuntu VM moeten geupdate worden.
> - Het bestand /etc/hosts op de VM moet aangepast worden, er moet een regel toegevoegd worden in /etc/hosts toe die naar je esxi server wijst. Noem de host esxi.
> - Een user `test` toevoegen op de VM
> - Een lokaal bestand wordt gekopieerd naar de VM
> - Maak een playbook dat /etc/ en /var/www/ back-upt, voeg een cronjob toe die dagelijks een back-up maakt en kopieer dit naar /tmp
> - Voer de code uit op de VM van opdracht 1

> Opdracht 3
>
> - Gebruik de repository van opdracht 1, maak een nieuwe branch `productie` aan
> - Deploy met Terraform drie VMs (vanuit 1 manifest) en zorg dat je in kunt loggen op deze vm's dmv een ssh-key. Zorg dat er bij het deployen van deze vm's automatisch een inventory wordt aangemaakt. Er zijn 2 webservers en 1 database server.

> Opdracht 4
>
> - In de `productie` branch:
> - Schrijf een playbook wat op twee webservers nginx installeert en op de database server mariadb. Het playbook moet gebruik maken van de groepen uit de inventory van Opdracht 3.

> Opdracht 5
>
> - In een branch `proxmox`
> - Schrijf een playbook dat van een Proxmox Hypervisor alle VM's backupped. Gebruik de volgende waardes: api gebruiker en wachtwoord is `test`, de api host is node1, en de storage waarop de backups opgeslagen moeten worden heet `backup_vm`. Voor deze opdracht moet de juiste proxmox community module gebruikt worden.
> - Je kunt dit playbook uiteraard niet testen.

Lever de code van opdracht 4 deze week in op Brightspace.
