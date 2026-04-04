# Cyber-Ops Logbook

> **Apprentissage technique (Linux / sécurité) au service d’une démarche GRC : comprendre, vérifier et traduire les risques techniques en décisions concrètes.**

---

## Objectif

Développer une compréhension opérationnelle des systèmes pour :
* **Analyser** des situations de sécurité réelles.
* **Vérifier** l’application effective des contrôles de sécurité.
* **Renforcer** la pertinence des recommandations en gouvernance (GRC).

---

## Positionnement & Expertise

* **Traduction des risques techniques** en enjeux métiers et business.
* **Vérification terrain** des pratiques de sécurité (Hardening, accès, exposition).
* **Interface opérationnelle** entre exigences réglementaires (RGPD, NIS2, ISO 27001) et réalité technique.

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
## Organisation du dépôt
Chaque section documente les concepts techniques, les patterns de résolution rencontrés et leur traduction en enjeux GRC.

[Dossier Bandit](./bandit/): Concepts clés et patterns de résolution (Linux & System Security).

Dossier leviathan : Analyse de la logique de privilèges et exploitation de binaires.

[Dossier Natas](./natas/): Vulnérabilités web et bonnes pratiques (Top 10 OWASP).

Dossier krypton : Bases de la cryptographie appliquée à la protection des actifs.

_**Note d'éthique** : Aucun secret ou mot de passe n'est publié afin de préserver l'intégrité des plateformes d'apprentissage et de démontrer mes réflexes de protection des données sensibles._
