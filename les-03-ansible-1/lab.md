#### LAB

Zorg dat je met Terraform 3 VM's hebt uitgerold en voer daarop de volgende opdrachten uit:

> Opdracht 1 — Repo & bereikbaarheid: Inventory + ad-hoc vs module
> 
> Maak repo aan, voeg ansible.cfg toe en wijs daarin je inventory.ini aan. 
> Bouw inventory.ini met groepen app en db, plus all:vars voor ansible_user en ansible_ssh_private_key_file. Gebruik FQDN of IP’s.
> Toon het verschil tussen:
> - ad-hoc met -a (bijv. klassiek ping -c 1)
> - en module-aanpak met ansible.builtin.ping (voorkeur). Leg uit waarom de module te verkiezen is (idempotentie/feedback). 
> 
> Deliverables:
> - ansible.cfg, inventory.ini.
> - Terminal-output (in README of als .txt) van een mislukte en gelukt(e) ping (tip: -vvvv bij fouten). 
>
> Opdracht 2 — Packages & services: eerste playbook met FQMN en become
>
> Schrijf playbooks/02_packages_services.yml met twee taken voor één groep (kies app of db):
> - Installeer een pakket.
> - Start + enable de bijbehorende service.
>
> Run het playbook twee keer en laat zien dat de tweede run changed=0 geeft (idempotentie).
> Voeg tags toe (bv. packages, services) en demonstreer --tags en --check --diff.
> 
> Deliverables:
> - Playbookbestand en console-output (screenshot) van run 1 en run 2.
>
> Opdracht 3 — Meerdere groepen in één draaiboek
>
> Maak playbooks/03_multi_group.yml met twee plays:
> - hosts: app → installeer chrony + start/enable service.
> - hosts: db → installeer curl (geen service nodig).
>
> Gebruik group_vars/app.yml en group_vars/db.yml om pakketnamen of instellingen per groep te parametriseren.
> Voeg een dry-run voorbeeld toe en noteer bijzondere observaties (bijv. wanneer --check niet alles kan voorspellen).
>
> Deliverables:
> - Playbook + group_vars.
> - Korte README-sectie met het commando om het playbook te draaien en uitleg over de variabelen.
>
> Opdracht 4 — Idempotence & guardrails: bewuste keuzes afdwingen
>
> Bouw playbooks/04_idempotence_guardrails.yml met taken die:
> Een bestand aanmaken via een module die daar het meest geschikt voor is.
> Een configbestand kopiëren en aantonen dat de tweede run nothing-to-do is.
> Voeg assertions toe op verwachte facts (bv. “bestand bestaat”, “service is enabled”).
> Documenteer in docs/decisions/… wanneer je nooit shell zou gebruiken en wanneer wel, met motivatie uit de theorie. 
>
> Deliverables:
> - Playbook + eventuele templates/bestanden.
> - Console-output van twee opeenvolgende runs.
> - Decisions document in versiebeheer
>
> Opdracht 5 — Release-play & Git-flow: branches, PR’s en releases
> Maak playbooks/05_release_play.yml die:
> Met hosts: all een smoketest doet.
> Daarna conditioneel de juiste play uit eerdere opdrachten aanroept (keuze is aan jullie; motiveer).
> Werk met feature-branches per opdracht (feat/inventory, feat/packages, …). Maak PR’s naar main. Voeg in elke PR:
> - korte samenvatting van de change,
> - verwijzing naar de beslisnotitie,
> - screenshots/uitvoer van --check, en
> - motivatie voor FQMN/become/tags. 
>
> Sluit af met een release-tag (bijv. v0.1.0) zodra 05_release_play.yml op alle hosts clean draait (2e run changed=0).
>
> Deliverables
> - 05_release_play.yml + bewijs van runs.
> - Overzicht van branches, PR-links (of screenshots), en één release-tag.

Zorg dat je code van alle opdrachten in versiebeheer staat.