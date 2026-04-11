# Security Ops Logbook
![Cybersecurity](https://img.shields.io/badge/Cybersecurity-GRC%20%7C%20Linux%20Security-red)
![Tool](https://img.shields.io/badge/Tool-Linux%20%7C%20Bash%20%7C%20OverTheWire-blue)
![Level](https://img.shields.io/badge/Level-Intermediate-orange)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow)

> **Apprentissage technique (Linux / sécurité) au service d’une démarche GRC : comprendre, vérifier et traduire les risques techniques en décisions concrètes.**

---

## Objectif

Développer une compréhension opérationnelle des systèmes pour :
* **Analyser** des situations de sécurité réelles.
* **Vérifier** l’application effective des contrôles de sécurité.
* **Renforcer** la pertinence des recommandations en gouvernance (GRC).

---

## Parcours Hands-on

### [OverTheWire — Bandit (Niveau 32 - Terminé)](https://overthewire.org/wargames/bandit/) | [Consulter mes notes](./bandit/)
*Focus : Fondamentaux Linux, permissions, gestion des accès et exposition des données.*

| Compétence technique | Lecture GRC / Audit |
| :--- | :--- |
| **Permissions & SUID** | Risque d’escalade de privilèges, principe du moindre privilège (IAM). |
| **Recherche de données (`grep`, `find`)** | Détection d'exposition involontaire d’informations sensibles. |
| **Réseau & services (`nc`, ports)** | Maîtrise de la surface d’exposition et contrôle des flux. |
| **SSH / OpenSSL** | Sécurisation des échanges et intégrité des communications. |
| **Bash scripting** | Automatisation de contrôles de sécurité (MCS). |

---

## Exemples de vérification (Audit & Prévention)

```bash
# Détection de fichiers à privilèges élevés (Risque d'élévation de privilèges)
find / -perm -4000 -type f 2>/dev/null

# Recherche de secrets en clair dans les configurations (Audit d'hygiène numérique)
grep -rEi "password|token|secret" /etc/ 2>/dev/null
```
---
### [OverTheWire — Krypton (Level 6/7 — En cours)](https://overthewire.org/wargames/krypton/) | [Consulter mes notes](./krypton/)

*Focus : Cryptographie appliquée — de César au stream cipher, en passant
par l'analyse de Kasiski et les générateurs pseudo-aléatoires faibles.*

| Compétence technique | Lecture GRC / Audit |
| :--- | :--- |
| **Analyse de fréquences (mono & polyalphabétique)** | Détection d'algorithmes obsolètes ou mal implémentés (CWE-327). |
| **Méthode de Kasiski & indice de coïncidence** | Identification de la réutilisation de clé — Key Reuse, principe du OTP. |
| **Attaque par plaintext connu sur stream cipher** | PRNG faible = keystream prévisible — exigence de CSPRNG en production (CWE-338). |
| **Script Python d'analyse cryptographique** | Automatisation des contrôles de sécurité — vérification de la robustesse des choix crypto lors d'un audit. |

---
### [OverTheWire — Leviathan (Level 4/8 — En cours)](https://overthewire.org/wargames/leviathan/) | [Consulter mes notes](./leviathan/)

*Focus : Analyse de binaires, escalade de privilèges SetUID, vulnérabilités TOCTOU.*

| Compétence technique | Lecture GRC / Audit |
| :--- | :--- |
| **Reverse engineering (`strings`, `ltrace`)** | Détection de credentials hardcodés dans les exécutables (CWE-798). |
| **Analyse `objdump`** | Compréhension de la logique d'autorisation — où le check s'arrête, où le risque commence. |
| **Faille TOCTOU + injection d'arguments** | Failles de conception sur les binaires privilégiés — impact escalade de privilèges (CWE-367). |
| **Binaires SetUID** | Surface d'attaque liée aux droits élevés — audit PoLP et revue des exécutables sensibles. |

---
### [OverTheWire — Natas (Level 12/34 — En cours)](https://overthewire.org/wargames/natas/) | [Consulter mes notes](./natas/)

*Focus : Vulnérabilités web côté serveur — contrôle d'accès, injections,
cryptographie faible. Mapping OWASP Top 10.*

| Compétence technique | Lecture GRC / Audit |
| :--- | :--- |
| **Reconnaissance & exposition (source HTML, robots.txt, directory listing)** | Fuite d'information involontaire — A01 Broken Access Control, hygiène de déploiement. |
| **Header spoofing & auth bypass cookie** | Contrôles d'accès côté client non fiables — A01, principe de validation serveur. |
| **LFI & Command Injection (RCE)** | Paramètres non filtrés = exécution arbitraire — A03 Injection (CWE-78 / CWE-22). |
| **Reverse engineering d'encodage & cryptanalyse XOR** | Clé statique + encodage réversible ≠ chiffrement — A02 Cryptographic Failures (CWE-327). |

---
## Organisation du dépôt
Chaque section documente les concepts techniques, les patterns de résolution rencontrés et leur traduction en enjeux GRC.

[Dossier Bandit](./bandit/): Concepts clés et patterns de résolution (Linux & System Security).

[Dossier Krypton](./krypton/): Bases de la cryptographie appliquée à la protection des actifs.

[Dossier Leviathan](./leviathan/): Analyse de la logique de privilèges et exploitation de binaires.

[Dossier Natas](./natas/): Vulnérabilités web et bonnes pratiques (Top 10 OWASP).

_**Note d'éthique** : Aucun secret ou mot de passe n'est publié afin de préserver l'intégrité des plateformes d'apprentissage et de démontrer mes réflexes de protection des données sensibles._
