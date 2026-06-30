# Cyber-Ops Logbook

> **Transformer des patterns techniques en décisions de gouvernance.**  
> *From technical patterns to governance decisions.*

> > A structured cybersecurity knowledge base translating technical attack patterns into governance and risk insights.
>> Key module: [Risk Pattern Overview](./docs/risk-patterns.md)

---

## Vision

Ce projet structure l’analyse de la sécurité à partir de l’observation de mécanismes techniques concrets issus de wargames et de labs.

L’objectif est de relier ces patterns à leur impact sur les contrôles, la gestion des risques et la gouvernance de la sécurité.

Il ne s’agit pas de documenter des exploits, mais de construire une lecture opérationnelle des vulnérabilités permettant d’alimenter des décisions de sécurité plus robustes et mieux alignées avec les environnements réels.

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

### Risk Pattern Mapping

Une cartographie des principaux patterns de vulnérabilités techniques observés dans les labs et wargames est disponible ici :

➡️ [Panorama des patterns de risques](./docs/risk-patterns.md)

Ce référentiel synthétise les vulnérabilités récurrentes et leur lecture en termes de contrôle, de gouvernance et de gestion des risques.

---

## Principes

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