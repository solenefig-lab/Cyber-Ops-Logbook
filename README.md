# Cyber-Ops Logbook

> **Transformer des patterns techniques en décisions de gouvernance.**  
> *From technical patterns to governance decisions.*

---

## Vision

La gouvernance cybersécurité est plus pertinente lorsqu'elle s'appuie sur une compréhension concrète des mécanismes techniques qu'elle cherche à maîtriser.

Ce dépôt documente des investigations réalisées sur des **CTF**, **wargames** et **laboratoires de cybersécurité** afin d'identifier des **patterns techniques réutilisables**, d'analyser leurs implications en matière de sécurité et de les traduire en enseignements utiles pour la gouvernance, la gestion des risques et les contrôles de sécurité.

L'objectif n'est **pas** de publier des solutions de challenges, mais de construire une base de connaissances reliant les observations techniques aux décisions de gouvernance.

---

## Démarche

```text
          Pattern technique observé
                    │
                    ▼
      Compréhension du mécanisme
                    │
                    ▼
     Qualification de la faiblesse
         (CWE / OWASP si pertinent)
                    │
                    ▼
       Analyse des impacts sécurité
                    │
                    ▼
 Recommandations de protection et contrôles
                    │
                    ▼
      Enseignements pour la gouvernance
```

---

## Contenu du dépôt

### OverTheWire - Patterns techniques

Les wargames **OverTheWire** sont utilisés comme terrain d'observation pour identifier des mécanismes récurrents de sécurité.

Les analyses couvrent notamment :

- Linux Security
- Permissions et gestion des privilèges
- Réseaux
- Cryptographie
- Authentification
- Analyse de binaires
- Git Security
- Automatisation

Chaque document synthétise :

- les patterns techniques observés ;
- leur lecture sécurité ;
- leur traduction en enjeux de gouvernance ;
- les bonnes pratiques de protection.

➡️ **Consulter le dossier [OverTheWire](./OverTheWire/README.md)**

---

### HackerDNA - Études de cas

Les laboratoires **HackerDNA** permettent d'analyser des chaînes d'attaque réalistes dans un environnement contrôlé.

Chaque write-up documente :

- la chaîne d'attaque ;
- les techniques employées ;
- les vulnérabilités identifiées ;
- les références CWE et OWASP lorsque pertinentes ;
- les recommandations de remédiation ;
- les enseignements pour la gouvernance.

Les analyses couvrent notamment :

- Web Security
- API Security
- Linux Security
- Network Security
- Privilege Escalation
- SQL Injection
- Command Injection
- Authentication & Access Control
- Information Disclosure
- Binary Analysis

➡️ **Consulter le dossier [HackerDNA](./HackerDNA/README.md)**

🔗 **Profil public HackerDNA (classement en temps réel)**  
[Profil Solene](https://hackerdna.com/fr/users/solene)

---

## 🎯 Principes

Ce dépôt repose sur quatre principes :

- **Comprendre avant d'exploiter.**
- **Capitaliser des patterns techniques réutilisables.**
- **Transformer les observations techniques en décisions de gouvernance.**
- **Privilégier une approche défensive, responsable et orientée maîtrise des risques.**

---

## Publication responsable

Afin de préserver l'intégrité des plateformes d'apprentissage :

- aucun flag n'est publié ;
- aucun mot de passe ou secret n'est divulgué ;
- certaines informations sont volontairement anonymisées ;
- les analyses privilégient la compréhension des mécanismes plutôt que la reproduction complète des solutions.

---

## 🇬🇧 English Summary

> **From technical patterns to governance decisions.**

Cyber-Ops Logbook is a cybersecurity knowledge base documenting reusable technical patterns extracted from CTFs, wargames and security labs.

Rather than publishing challenge solutions, the repository focuses on understanding how vulnerabilities emerge, analysing recurring security patterns and translating technical findings into governance, risk management and defensive security insights.

The repository currently includes:

- **OverTheWire:** reusable Linux, system, networking and cryptography security patterns.
- **HackerDNA:** end-to-end security case studies with governance-oriented analysis and defensive recommendations.

---

## 🤝 Contributions

Suggestions, corrections and discussions are welcome through Issues and Pull Requests.