# OverTheWire — Leviathan (Patterns & Concepts)

> Objectif : Extraire les patterns techniques clés et leur lecture 
> sécurité (GRC), sans divulguer de solutions.

---

## 1. Exposition de données dans fichiers non surveillés

**Niveaux concernés : 0 → 1**

- Exploration de répertoires cachés
- Lecture de fichiers à permissions restreintes
- Recherche de credentials dans des données non structurées (HTML, logs)

**Pointers**
```bash
ls -la
grep -i "password" fichier.html
```

**Lecture sécurité**
- **Risque :** Credentials stockés en clair dans des fichiers accessibles au groupe.
- **Enjeu :** Contrôle d'accès sur les fichiers sensibles, principe du moindre privilège (PoLP).

---

## 2. Reverse engineering de binaires & secrets embarqués

**Niveaux concernés : 1 → 2**

- Analyse statique d'un exécutable (`strings`, `ltrace`)
- Interception d'appels système en temps réel
- Identification de mots de passe hardcodés dans le binaire

**Pointers**
```bash
strings ./binaire          # repère les chaînes lisibles
ltrace ./binaire           # intercepte strcmp() en live
```

**Lecture sécurité**
- **Risque :** Hardcoded credentials dans les exécutables (CWE-798 / CWE-312).
- **Enjeu :** Politique de gestion des secrets, interdiction des credentials en dur, usage d'un vault en production.

---

## 3. Vulnérabilité TOCTOU & injection d'arguments

**Niveaux concernés : 2 → 3**

- Analyse d'un binaire SetUID via `strings`, `ltrace`, `objdump`
- Faille TOCTOU : fenêtre entre vérification (`access()`) et exécution (`system()`)
- Injection d'arguments via nom de fichier avec espace ou séparateur `;`
- Escalade de privilèges via shell spawné avec droits effectifs

**Pointers**
```bash
strings ./binaire          # repère /bin/cat %s et access()
ltrace ./binaire           # confirme la logique check → execute
objdump -d ./binaire       # localise access@plt puis snprintf@plt
touch "nom fichier"        # crée le leurre pour access()
ln -s /cible nom           # lien symbolique pour cat
```

**Lecture sécurité**
- **Risque :** TOCTOU (CWE-367) + injection de commande (CWE-78) via construction de commande shell sans échappement.
- **Enjeu :** Validation stricte des entrées, revue de code sur les binaires privilégiés, remplacement de `system()` par des appels directs.

---

## 4. Credentials obfusqués en mémoire

**Niveaux concernés : 3 → 4**

- Analyse `strings` sur un binaire compilé
- Identification de chaînes fragmentées / obfusquées
- Lecture `objdump` pour reconstituer la logique de comparaison

**Pointers**
```bash
strings ./binaire          # repère fragments de chaînes
objdump -d ./binaire       # analyse la logique strcmp
```

**Lecture sécurité**
- **Risque :** L'obfuscation n'est pas du chiffrement — les secrets restent récupérables par analyse statique (CWE-261).
- **Enjeu :** Aucun secret ne doit résider dans un binaire, même fragmenté. Chiffrement et gestion externe obligatoires.

---

## Synthèse

**Patterns clés acquis :**
- Analyser statiquement avant d'exécuter (`strings`, `objdump`).
- Intercepter dynamiquement pour confirmer (`ltrace`).
- Identifier la fenêtre TOCTOU : où s'arrête le check, où commence l'exécution.
- Tout espace ou caractère non échappé dans une commande shell est une surface d'injection.

**Positionnement**

Ces exercices sont abordés comme un terrain d'entraînement pour :
- Comprendre les mécanismes d'escalade de privilèges réels.
- Identifier les failles de conception dans les binaires privilégiés.
- Renforcer une approche GRC pragmatique : savoir ce qu'un auditeur technique peut extraire d'un système.

**N.b. :**
- Pas de solutions ni de mots de passe divulgués.
- Approche méthodologique uniquement.
- Environnement d'apprentissage contrôlé.
