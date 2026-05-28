# HackerDNA Write-ups

![Cybersecurity](https://img.shields.io/badge/Cybersecurity-Forensics%20%7C%20Incident%20Response-blueviolet)
![Tool](https://img.shields.io/badge/Tool-PHP%20%7C%20Logs%20%7C%20HackerDNA-blue)
![Level](https://img.shields.io/badge/Level-Intermediate-orange)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow)

Ce dossier centralise mes analyses et démarches des défis et labs de la plateforme [HackerDNA](https://hackerdna.com/). 


## Index des Investigations

| Challenge | Difficulté | Domaines d'Audit |
| :--- | :--- | :--- |
| [**FiPloit**](./FiPloit.md) | Facile | Arbitrary File Upload, RCE, PrivEsc |
| [**Clear Desk**](./ClearDesk.md/) | Moyen | IDOR / BOLA, API Security, Path Traversal |
| [**Host Hijack**](./HostHijack.md/) | Moyen | HTTP Headers, Password Reset Poisoning, Command Injection, PrivEsc   |
| [**TechNova Infiltration**](./TechnovaInfiltration.md/) | Moyen | Web Enumeration, SSH, Password Cracking, PrivEsc, Linux Security, CVE Exploitation |
| [**Traversed**](./Traversed.md/) | Moyen | Git Exposure, Source Code Disclosure, Credential Leakage, SSH, Python Library Hijacking, PrivEsc |


---

##  Approche GRC & Forensic
Chaque investigation est structurée pour répondre à trois objectifs :
1.  **Identification du vecteur** : Comprendre comment l'attaquant a pénétré le périmètre.
2.  **Analyse de l'impact** : Évaluer la criticité de la vulnérabilité (CWE/OWASP).
3.  **Remédiation** : Proposer des mesures de protection concrètes (PSSI, durcissement, monitoring).

[👤 Consulter mon profil public HackerDNA](https://hackerdna.com/users/solene)

> **Note d'éthique** : Conformément aux bonnes pratiques de la communauté et pour préserver l'intégrité des plateformes d'apprentissage, aucun secret (flag) ou mot de passe n'est publié. L'objectif est de documenter la méthodologie d'audit et de démontrer mes réflexes de protection des données sensibles.