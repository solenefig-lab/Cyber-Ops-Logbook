# HackerDNA Write-ups

![Cybersecurity](https://img.shields.io/badge/Cybersecurity-Offensive%20Security%20%7C%20GRC-red)
![Tool](https://img.shields.io/badge/Platform-HackerDNA-blue)
![Level](https://img.shields.io/badge/Scope-Web%20%7C%20Linux%20%7C%20Network-orange)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow)

> **Des chaînes d'attaque aux recommandations de gouvernance.**

---

## Positionnement

Les laboratoires **HackerDNA** sont abordés comme des études de cas reproduisant des scénarios d'attaque réalistes.

Chaque investigation vise à comprendre la chaîne d'attaque dans son ensemble, à identifier les vulnérabilités exploitées et à traduire les observations techniques en recommandations utiles pour la gouvernance, la gestion des risques et le renforcement des contrôles de sécurité.

Les solutions complètes ne sont volontairement pas publiées ; l'accent est mis sur la méthodologie d'analyse et les enseignements réutilisables.

---

## Investigations

| Challenge | Difficulté | Domaines d'Audit |
| :--- | :--- | :--- |
| [**Alpwned**](Alpwned.md) | Moyen | SQL Injection, File Permissions, Privilege Escalation |
| [**AlVault**](./AlVault.md) | Moyen | Web, Information Leakage, Command Injection |
| [**FiPloit**](./FiPloit.md) | Facile | Arbitrary File Upload, RCE, PrivEsc |
| [**Clear Desk**](./ClearDesk.md/) | Moyen | IDOR / BOLA, API Security, Path Traversal |
| [**Host Hijack**](./HostHijack.md/) | Moyen | HTTP Headers, Password Reset Poisoning, Command Injection, PrivEsc   |
| [**TechNova Infiltration**](./TechnovaInfiltration.md/) | Moyen | Web Enumeration, SSH, Password Cracking, PrivEsc, Linux Security, CVE Exploitation |
| [**Ping Pwn**](./PingPwn.md/) | Défi | Command Injection, Web Exploitation, Service Discovery, Network Security |
| [**Traversed**](./Traversed.md/) | Moyen | Git Exposure, Source Code Disclosure, Credential Leakage, SSH, Python Library Hijacking, PrivEsc |

---

## Méthodologie d'analyse

Chaque write-up suit une démarche structurée.

```text
Reconnaissance
        │
        ▼
Identification du vecteur d'attaque
        │
        ▼
Analyse technique
        │
        ▼
Qualification de la vulnérabilité
(CWE / OWASP lorsque pertinent)
        │
        ▼
Analyse des impacts
        │
        ▼
Recommandations de remédiation
        │
        ▼
Enseignements pour la gouvernance
```

---

## Ce que documente chaque investigation

- Chaîne d'attaque
- Techniques d'exploitation
- Vulnérabilités identifiées
- Références CWE et OWASP lorsque pertinentes
- Analyse des impacts
- Mesures de remédiation
- Enseignements pour la gouvernance

---

## Profil HackerDNA

[👤 Retrouvez mon profil public HackerDNA *(classement en temps réel)*](https://hackerdna.com/users/solene)

---

## Publication responsable

Conformément aux bonnes pratiques de la communauté :

- aucun flag n'est publié ;
- aucun secret ou mot de passe n'est divulgué ;
- certaines informations sont volontairement anonymisées ;
- les analyses privilégient la compréhension des mécanismes d'attaque et les mesures de protection plutôt que la reproduction complète des solutions.

---

## 🇬🇧 English Summary

These write-ups document realistic attack scenarios performed on HackerDNA laboratories.

Rather than publishing walkthroughs, each case study analyses the attack chain, identifies recurring security weaknesses and translates technical findings into governance, risk management and defensive security recommendations.