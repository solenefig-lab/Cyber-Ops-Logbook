# Technical Security Assessment Playbook

Ce playbook met en relation les principales actions réalisées lors d'un audit technique (commandes, outils, payloads) avec les observations attendues, les risques révélés et les contrôles de sécurité qui devraient les prévenir ou les détecter.
Il complète les études de cas (HackerDNA, OverTheWire) en fournissant une lecture transversale des pratiques d’audit.
Chaque combinaison « Indice recherché → Risque potentiel → Contrôle GRC » de ce playbook se rattache aux patterns décrits dans le [Panorama des patterns de risques](./risk-patterns.md), permettant de relier les observations d’audit de terrain aux référentiels CWE / OWASP et aux décisions de gouvernance.


## Des actions techniques aux décisions GRC

- Les commandes de **reconnaissance réseau** (nmap, nc) servent à évaluer la gestion de la surface d’exposition, l’inventaire des actifs et le durcissement des services.
- Les commandes de **reconnaissance web** (curl, robots.txt, ffuf, gobuster) testent l’hygiène de déploiement, la suppression des artefacts de développement et la gestion des informations exposées.
- Les commandes **Linux** (find, sudo -l, ps, ls, whoami) évaluent l’application du principe du moindre privilège, la revue des droits, la gestion des binaires SUID et des scripts sensibles.
- Les tests **web / SQL / LFI** (`’ OR 1=1`, traversal, sqlmap) valident l’efficacité des contrôles de validation des entrées, de design sécurisé et de protection contre les injections.
- L’identification de hash faibles (MD5) s’inscrit dans la revue des choix cryptographiques et des politiques IAM (robustesse des mots de passe, stockage des secrets).

Ce playbook peut être utilisé :

- comme check-list d’audit technique,
- comme support de mapping entre constats d’intrusion et contrôles GRC,
- comme guide pour dériver des exigences de contrôle (ISO 27001, OWASP, NIS2, CIS, etc.).

_Note: Les exemples présentés dans ce document sont destinés à des environnements de laboratoire, de formation ou d'audit explicitement autorisés._

---

## Table de mapping gestes → contrôles

| Phase          | Commande / Entrée                                      | Indice recherché                                           | Risque potentiel                            | Contrôle GRC / principe sécurité                         |
|----------------|--------------------------------------------------------|------------------------------------------------------------|---------------------------------------------|-------------------------------------------------------|
| Recon Réseau   | `nmap $IP`                                             | Ports / services ouverts                                   | Surface d’exposition non maîtrisée         | Inventaire des actifs, gestion de la surface d’attaque |
| Recon Réseau   | `nmap -sV --script ftp-anon -p 21 $TARGET`             | Service FTP accessible en anonyme                          | Accès non autorisé, fuite de données       | Contrôles d’accès aux services, durcissement des services legacy |
| Recon Réseau   | `nc $TARGET 21`                                       | Bannière, type de serveur, mode d’authentification        | Fuite d’informations, services non durcis  | Politique d’exposition des services, bannering contrôlé |
| Recon Web      | `curl -I $IP`                                          | En-têtes HTTP (Server, TLS, sécurité)                      | Mauvaise configuration HTTP/TLS            | Configuration HTTP/TLS, headers de sécurité            |
| Recon Web      | `curl -s $IP` (code source)                            | Commentaires, chemins internes, infos sensibles            | Information Disclosure                      | Hygiène de déploiement, maîtrise des infos exposées    |
| Recon Web      | `curl $IP/robots.txt`                                  | Répertoires “sensibles” listés                             | Cartographie involontaire de zones critiques | Gestion de la surface exposée, non-usage de robots.txt comme pseudo-contrôle |
| Enum Web       | `ffuf` / `gobuster`                                   | Fichiers / répertoires cachés (admin, backup, dev…); Interfaces d'administration (manager, host-manager, admin, console...)    | Accès à des interfaces ou fichiers non protégés | Gestion du cycle de vie applicatif, suppression des artefacts dev; Inventaire des interfaces d'administration, restriction d'accès, suppression des interfaces inutiles |
| Enum Web | nmap --script http-enum,http-auth,http-title,http-headers | Interface Tomcat Manager, authentification Basic, version exposée | Exposition d'une interface d'administration | Restriction des interfaces d'administration (VPN, filtrage IP, RemoteAddrValve), inventaire des interfaces critiques |
| Enum Linux     | `ls -la`                                               | Droits excessifs, fichiers cachés, propriétaires anormaux | Droits inappropriés, fuite de données      | Principe du moindre privilège, séparation des responsabilités |
| Enum Linux     | `whoami`                                               | Compte effectif (www-data, root, user…)                   | Compte technique sur-privilégié            | Gestion des comptes, séparation des comptes privilégiés |
| Enum Linux     | `find / -perm -4000 -type f 2>/dev/null`              | SUID inhabituel, binaire sensible                          | Privilege Escalation                        | Principe du moindre privilège, revue des SUID                        |
| Enum Linux     | `sudo -l`                                              | Commandes exécutables en sudo sans contrôle suffisant      | Escalade via scripts / binaires privilégiés | Revue périodique des droits sudo, contrôle des scripts |
| Enum Linux     | `ps aux`+ `grep setup`                                  | Processus sensibles (scripts install, setup…)              | Processus non maîtrisés, backdoors         | Gestion des services, durcissement des scripts système |
| Exploit Web    | Payload `’ OR 1=1`                                     | Comportement anormal (auth bypass, erreur SQL)            | SQL Injection, auth bypass                  | Validation des entrées, protections anti-SQLi          |
| Exploit Web    | `http://$IP/admin/logs?file=../../../../etc/passwd`    | Lecture de fichiers système (etc/passwd)                   | LFI / path traversal                        | Contrôle d’accès aux fichiers, validation des chemins  |
| Exploit Web    | `sqlmap -r req.txt --technique=B --dbms=SQLite --tables --batch` | Extraction automatique de données / structure DB | Compromission de données par SQLi          | Tests d’intrusion contrôlés, couverture vulnérabilités SQL, durcissement applicatif |
| Analyse Crypto | Hash MD5 observé                                      | Usage de MD5 pour mots de passe / intégrité                | Cryptographic Failure (hash faible)         | Politique de hashage, choix d’algos robustes, IAM      |
| Analyse Configuration | Observation de l'interface Tomcat Manager | Fonction de déploiement WAR accessible aux utilisateurs autorisés | Déploiement de code arbitraire via une interface d'administration | Gouvernance des rôles d'administration, séparation des privilèges, revue périodique des comptes privilégiés |
| Exploit Fichiers | `cat` (config, logs, /etc/passwd…)                  | Accès à des fichiers sensibles                             | Fuite d’informations, exposure of secrets   | Contrôle d’accès aux fichiers, protection des données  |

Chaque ligne de ce tableau illustre un lien direct entre un geste technique d’audit (commande, payload) et :
- l’indice recherché sur le terrain,
- le risque qu’il révèle,
- le contrôle GRC qui devrait prévenir ou détecter cette situation.

Les risques mentionnés dans ce tableau sont détaillés dans le [Panorama des patterns de risques](./risk-patterns.md), qui fournit pour chaque faiblesse les correspondances CWE, OWASP, les impacts sécurité et les recommandations de remédiation.


---
## Méthode

Ce workflow résume la manière dont une observation technique issue d’un lab est transformée en décision de gouvernance :


```Workflow

Observation
      │
      ▼
Weakness
      │
      ▼
CWE
      │
      ▼
OWASP
      │
      ▼
Risk
      │
      ▼
Security Control
      │
      ▼
Governance Decision
```

---

## License

© Solène Figueiredo

Licensed under CC BY 4.0.