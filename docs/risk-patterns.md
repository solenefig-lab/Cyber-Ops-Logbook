# Panorama des patterns de risques techniques

## Lecture globale

Ce document synthétise des patterns de risques observés à travers des environnements de type wargames et labs de sécurité.

L’objectif est de relier des vulnérabilités techniques à des implications en matière de contrôle, de gouvernance et de gestion des risques.

Ce document constitue le référentiel central du dépôt.

Les observations techniques issues du **[Technical Security Assessment Playbook](technical-security-assessment-playbook.md)** et des études de cas **[HackerDNA](../HackerDNA/README.md)** et **[OverTheWire](../OverTheWire/README.md)** y sont consolidées sous forme de familles de risques, de recommandations de sécurité et de références aux principaux standards (CWE, OWASP Top 10).

---

## Index des risques

| Risque | CWE | Impact | Recommandation | Source |
|--------|-----|--------|----------------|--------|
| Command Injection | CWE-78 | Exécution de code arbitraire sur le système | Éviter les appels système directs, utiliser `subprocess.run()` avec `shell=False` et validation stricte | AIVault, HostHijack , PingPwn |
| SQL Injection | CWE-89 | Accès non autorisé aux données, contournement d’authentification | Requêtes préparées, validation côté serveur, SAST/DAST | Alpwned, Auth Bypass, Test d'Injection SQL |
| Information Disclosure | CWE-200 | Exposition de données sensibles et chemins internes | Nettoyage des environnements, suppression des fichiers de debug | AIVault, FiPloit |
| Exposition d'Information via Message d'Erreur | CWE-209 | Divulgation d'informations sur le fonctionnement interne de l'application (moteur SQL, structure des requêtes, erreurs) | Retourner des messages d'erreur génériques, journaliser les détails côté serveur, désactiver les erreurs détaillées en production  | Auth Bypass |
| Privilege Escalation | CWE-269 / 250 | Accès root ou élévation de privilèges | Moindre privilège, audit sudo, durcissement des permissions | FiPloit, HostHijack |
| Transmission d'informations sensibles en clair sur le réseau | CWE-319 | Expose identifiants et cookies de session à l'interception et attaques de type *Man-in-the-Middle* | Forcer HTTPS (TLS), redirection HTTP→HTTPS, activer HSTS, utiliser des cookies `Secure` et `HttpOnly` | Auth bypass |
| Python Library Hijacking | CWE-427 | Exécution de code arbitraire via dépendances | Sécuriser PYTHONPATH et chemins d’exécution | Traversed |
| File Upload Bypass | CWE-434 | Upload de webshell et compromission serveur | Validation stricte des extensions, renommage des fichiers | FiPloit |
| Insufficiently Protected Credentials | CWE-522 | Compromission d’accès SSH / fuite de clés | Interdire stockage de clés privées sur serveurs exposés | TechnovaInfiltration |
| Git Repository Exposure | CWE-552 | Fuite du code source et secrets | Bloquer l’accès au répertoire `.git` côté serveur | Traversed |
| BOLA / IDOR | CWE-639 | Fuite de données entre utilisateurs | Vérification des autorisations côté serveur sur chaque objet | ClearDesk |
| Password Reset Poisoning | CWE-640 | Prise de contrôle de compte | Utilisation d’un host statique et sécurisé | HostHijack |


---

## Cartographie des contrôles

| Risque    | CWE     | OWASP Top 10   | Contrôle principal    | Détection    |
| ------------------ | ------- | ----------------- | ----------------- | ----------------- |
| Command Injection | CWE-78  | A03 – Injection  | Validation des entrées    | SAST, revue de code    |
| SQL Injection  | CWE-89  | A03 – Injection   | Requêtes préparées     | SAST, DAST, revue de code     |
| Information Disclosure   | CWE-200 | A05 – Security Misconfiguration | Durcissement des environnements   | Revue de configuration   |
| Error Message Disclosure  | CWE-209 | A05 – Security Misconfiguration | Gestion sécurisée des erreurs     | Tests applicatifs    |
| Privilege Escalation | CWE-269 / 250 | A01 – Broken Access Control | Principe du moindre privilège, revue des permissions, durcissement des comptes et des privilèges | Audit des permissions, revue de configuration, tests de privilèges |
| Transmission d'informations en clair | CWE-319 | A02 – Cryptographic Failures     | HTTPS/TLS, HSTS    | Scan TLS, revue de configuration   |
| Python Library Hijacking | CWE-427 | A08 – Software and Data Integrity Failures | Sécurisation du PYTHONPATH, gestion des dépendances, chemins d'import maîtrisés | Revue de configuration, SAST, analyse des dépendances |
| File Upload Bypass   | CWE-434 | A04 – Insecure Design | Validation stricte du type et contenu des fichiers, stockage hors répertoire exécutable, renommage des fichiers   | Tests applicatifs, revue de code, tests d'upload |
| Insufficiently Protected Credentials | CWE-522 | A02 – Cryptographic Failures | Protection des secrets, stockage sécurisé des identifiants et clés, chiffrement lorsque nécessaire | Secret scanning, revue de configuration, audit des dépôts |
| Git Repository Exposure   | CWE-552 | A05 – Security Misconfiguration| Restriction d'accès aux répertoires sensibles | Scan de configuration  |
| BOLA / IDOR     | CWE-639 | A01 – Broken Access Control| Contrôle d'accès par objet   | Tests fonctionnels, tests d'autorisation |
| Password Reset Poisoning | CWE-640 | A07 – Identification and Authentication Failures | Génération sécurisée des liens de réinitialisation, validation stricte du domaine (Host), jetons à durée de vie limitée | Revue de code, tests fonctionnels du workflow de réinitialisation |

_Note : Les correspondances CWE ↔ OWASP Top 10 sont indicatives; une même faiblesse peut relever de plusieurs catégories selon son contexte d'exploitation._

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

## Synthèse des familles de risques observées

Les vulnérabilités observées convergent vers cinq familles principales :

- **Contrôle d'accès insuffisant**
  - Exemple : BOLA / IDOR, contournement d'authentification

- **Validation insuffisante des entrées**
  - Exemple : SQL Injection, Command Injection, File Upload Bypass

- **Exposition d'informations sensibles**
  - Exemple : messages d'erreur détaillés, données sensibles exposées

- **Gestion insuffisante des privilèges système**
  - Exemple : élévation de privilèges, permissions excessives

- **Gestion des secrets et intégrité du code**
  - Exemple : exposition de clés, dépendances ou bibliothèques compromises

---

## Référentiels utilisés

Les correspondances avec OWASP Top 10 se basent sur la version **OWASP Top 10:2021**.

Ce document utilise principalement :
- MITRE CWE pour classifier les faiblesses techniques ;
- OWASP Top 10:2021 pour relier ces faiblesses aux grandes familles de risques applicatifs.

---

## Finalité

Ce document structure une lecture des risques techniques sous un angle exploitable en audit, en gouvernance et en conception de contrôles de sécurité.

---

## Aller plus loin

Pour comprendre comment ces risques sont identifiés sur le terrain :

- consulter le **[Technical Security Assessment Playbook](./technical-security-assessment-playbook.md)** ;
- explorer les études de cas **[HackerDNA](../HackerDNA/README.md)** ;
- parcourir les analyses **[OverTheWire](../OverTheWire/README.md)**.

---

## License

© Solène Figueiredo

Licensed under CC BY 4.0.