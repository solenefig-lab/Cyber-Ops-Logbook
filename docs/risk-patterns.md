# Panorama des patterns de risques techniques

## Lecture globale

Ce document synthétise des patterns de risques observés à travers des environnements de type wargames et labs de sécurité.

L’objectif est de relier des vulnérabilités techniques à des implications en matière de contrôle, de gouvernance et de gestion des risques.

---

## Index des risques

| Risque | CWE | Impact | Recommandation | Source |
|--------|-----|--------|----------------|--------|
| Command Injection | CWE-78 | Exécution de code arbitraire sur le système | Éviter les appels système directs, utiliser `subprocess.run()` avec `shell=False` et validation stricte | PingPwn / HostHijack / AIVault |
| SQL Injection | CWE-89 | Accès non autorisé aux données, contournement d’authentification | Requêtes préparées, validation côté serveur, SAST/DAST | Alpwned |
| BOLA / IDOR | CWE-639 | Fuite de données entre utilisateurs | Vérification des autorisations côté serveur sur chaque objet | ClearDesk |
| Information Disclosure | CWE-200 | Exposition de données sensibles et chemins internes | Nettoyage des environnements, suppression des fichiers de debug | AIVault / FiPloit |
| Privilege Escalation | CWE-269 / 250 | Accès root ou élévation de privilèges | Moindre privilège, audit sudo, durcissement des permissions | FiPloit / HostHijack |
| File Upload Bypass | CWE-434 | Upload de webshell et compromission serveur | Validation stricte des extensions, renommage des fichiers | FiPloit |
| Sensitive Data Exposure | CWE-522 | Compromission d’accès SSH / fuite de clés | Interdire stockage de clés privées sur serveurs exposés | TechnovaInfiltration |
| Git Repository Exposure | CWE-552 | Fuite du code source et secrets | Bloquer l’accès au répertoire `.git` côté serveur | Traversed |
| Password Reset Poisoning | CWE-640 | Prise de contrôle de compte | Utilisation d’un host statique et sécurisé | HostHijack |
| Python Library Hijacking | CWE-427 | Exécution de code arbitraire via dépendances | Sécuriser PYTHONPATH et chemins d’exécution | Traversed |

---

## Lecture des patterns (extraits représentatifs)

### Command Injection (CWE-78)

**Description**  
Exécution de commandes système via entrées non contrôlées.

**Impact**  
Compromission complète de l’hôte.

**Recommandations**
- `subprocess.run()` avec `shell=False`
- validation stricte des entrées
- interdiction des concaténations de commandes

---

### SQL Injection (CWE-89)

**Description**  
Manipulation de requêtes SQL via entrées utilisateur.

**Impact**  
Accès non autorisé aux données et contournement d’authentification.

**Recommandations**
- requêtes préparées
- validation serveur
- tests SAST/DAST

---

### BOLA / IDOR (CWE-639)

**Description**  
Accès à des objets non autorisés par manipulation d’identifiants.

**Impact**  
Fuite de données multi-utilisateurs.

**Recommandations**
- contrôle d’accès côté serveur
- vérification par objet
- tests automatisés des permissions

---

## Synthèse

Les vulnérabilités observées convergent vers quatre familles principales :

- Contrôle d’accès insuffisant
- Mauvaise validation des entrées
- Exposition d’informations sensibles
- Mauvaise gestion des privilèges système

---

## Finalité

Ce document structure une lecture des risques techniques sous un angle exploitable en audit, en gouvernance et en conception de contrôles de sécurité.