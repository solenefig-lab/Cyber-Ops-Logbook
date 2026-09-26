# WriteUp : SSRF Attack - voler les identifiants des métadonnées cloud

**Plateforme :** HackerDNA  (https://hackerdna.com/labs/ssrf-attack-cloud-metadata-credentials)

**Catégorie :**  SSRF - IAM Crediential Theft - Metadata Service Abuse  
**Difficulté :** Moyen   
**Tags :** `SSRF``Cloud Penetration Testing``AWS IMDS``IMDSv2``IAM Credential Theft``Privilege Escalation``Metadata Service Abuse``API Enumeration`


---

## Scénario

Le laboratoire simule un SaaS, Previewly, qui génère des cartes d'aperçu URLs. L'application tourne sur une instance AWS EC2 avec un rôle IAM attaché.

---

## Chaîne d'Attaque
`[Reconnaissance & Énumération]` ➔ `[Analyse du Point d'Entrée]` ➔ `[SSRF]` ➔ 🚩 ➔ `[Enumération identifiants IAM]` ➔ `[Accès Serveur]` ➔ `[Abus métadonnées]` ➔ `[Escalade de Privilège]` ➔ 🚩

---

## Étape 1 - Reconnaissance & Énumération

La reconnaissance des ports identifie 4 ports ouverts, tous derrière Cloudflare, dont deux redirigent vers le port 80.

**Erreur et recadrage :** Au départ, la recherche s'est focalisée sur les 3 autres ports (443, 8080, 8443) en supposant qu'ils exposaient des services distincts. En réalité, le scan Nmap et les tests curl confirment que tous redirigent vers le port 80. 

L'étude de la page d'accueil révèle un SaaS d'aperçu de liens nommé Previewly. Le texte de la carte `B`uilt for scale confirme l'architecture : "Running on autoscaling cloud workers with an instance role for storage and cache access."

Deux indices du stack technologique sont présents en clair :

- Dans le `header` : le nom du service (Previewly) et sa fonction (Link previews as a service).  
- Dans le `body` : la documentation de l'API `/api/unfurl` avec un exemple de requête et de réponse, révélant le champ `body_preview` et le comportement côté serveur : "The worker fetches url for you and returns what it received.".

**Réflexe audit :** 
- Une application derrière un proxy ou CDN n'est pas protégée contre le SSRF : la requête part du serveur lui-même, pas du client.
- Un endpoint documenté publiquement qui fetche des URLs fournies par l'utilisateur est un candidat SSRF par conception.

---

## Étape 2 - Analyse du point d'entrée & SSRF

L'analyse du point d'entrée `/api/unfurl` confirme l'acceptation d'une URL fournie par l'utilisateur et un fetch côté serveur avec restitution du contenu dans le champ `body_preview`.

Le premier test logique est de déterminer si l'endpoint filtre les destinations: le serveur a récupéré l'URL interne et a renvoyé le HTML dans `body_preview`. Le SSRF est confirmé **in-band**, c.-a-d. que l'attaquant voit le résultat de la requête.

```bash

curl -s -X POST http://$IP/api/unfurl \
  -H "Content-Type: application/json" \
  -d '{"url":"http://127.0.0.1/"}'
```

Le second test vérifie si l'endpoint link-local des métadonnées cloud est accessible en utilisant l'endpoint de métadonnées cloud standard sur AWS   : l'IMDS répond; la destination n'est pas filtrée, l'énumération des métadonnées est possible.

```bash

curl -s -X POST http://$IP/api/unfurl \
  -H "Content-Type: application/json" \
  -d '{"url":"169.254.169.254"}'
  ```

**Erreur et recadrage :** la première requête a été envoyée sans le header `Content-Type: application/json`. L'API a répondu qu'aucune URL n'était fournie. Le body JSON n'est interprété que si ce header est présent.

L'identification du fournisseur cloud se fait par l'observation du contenu de l'IMDS, notamment la racine retourne `latest`, préfixe d'API propre à AWS. 
L'énumération cible ensuite le script de démarrage de l'instance, chemin standard AWS, frère de /meta-data/ sous /latest/.
Le script de démarrage expose le User Flag et une référence à une API interne protégée par un session token.

🚩 Flag User

**Réflexe audit :**
-  Vérifier systématiquement si l'API filtre les destinations : tester une URL contrôlée, puis 127.0.0.1, puis l'endpoint link-local.
- Une URL link-local doit être bloquée par défaut en environnement cloud : si elle répond, c'est une faille critique.
- Le correctif n'est pas la validation d'entrée seule, mais une allowlist stricte de destinations + blocage des plages internes et des endpoints de métadonnées cloud.


---

## Étape 3 - Énumération des identifiants IAM

 L'énumération cible les credentials IAM attachés à l'instance : le listing expose le nom du rôle attaché à l'instance. C'est un chemin standard de l'IMDS AWS.

```bash 

curl -s -X POST http://$IP/api/unfurl \
  -H "Content-Type: application/json" \
  -d '{"url":"http://169.254.169.254/latest/meta-data/iam/security-credentials/<ROLE>"}'
```
L'endpoint retourne un objet JSON contenant `AccessKeyId`, `SecretAccessKey`, `Token` et `Expiration`. Ces credentials temporaires permettent d'agir en tant que rôle IAM de l'instance.

L'extraction du token nécessite un double parsing JSON : le champ `body_preview` est une chaîne qui contient elle-même du JSON.

**Erreur et recadrage :** le token fait 170 caractères. Sa longueur a d'abord laissé penser à une troncature côté serveur, les tokens AWS STS réels étant plus longs. L'IMDS étant simulé, cette valeur est bien complète. 

**Réflexe audit :**
- Le parsing des réponses nécessite de traiter une chaîne JSON imbriquée : le champ `body_preview` contient lui-même du JSON échappé.
- Un rôle IAM attaché à une instance EC2 doit respecter le principe du moindre privilège et ne pas avoir accès à des secrets globaux si sa seule fonction est du stockage/cache local.

---

## Étape 4 - Accès au serveur interne & Abus de métadonnées

Le SSRF permet de découvrir l'API interne, mentionnée lors de la phase d'énumération du contenu IMDS, via le loopback.
L'API expose plusieurs endpoints protégés et déclare un schéma d'authentification de type `session-token`.

```bash
curl -s -X POST http://$IP/api/unfurl \
  -H "Content-Type: application/json" \
  -d '{"url":"http://127.0.0.1/xxx/"}'
  ```

**Erreur et recadrage :** une part importante du temps a été consacrée à tenter de transmettre le token au serveur interne via le SSRF (query parameters, headers, cookies, corps, injection CRLF). Toutes ces voies ont échoué. 
Le serveur interne était en réalité accessible directement depuis l'extérieur, sans passer par `/api/unfurl`, qui ne traite que le champ url.

L'authentification s'effectue avec le token IAM récupéré à l'étape précédente, dans un header standard. La réponse confirme l'identité assumée (authorized: true), avec le rôle et l'ARN de l'instance.

```bash
curl -s http://$IP/xxx/whoami \
  -H "Authorization: Bearer $TOKEN"
```

Les secrets sont ensuite listés puis lus individuellement
La liste expose plusieurs entrées, dont un secret dont le nom évoque directement l'objectif du lab.


```bash
curl -s http://$IP/control-plane/xx/xxx/<SECRET> \
  -H "Authorization: Bearer $TOKEN"
```

🚩 Flag Root

**Réflexe audit :**
- Tester systématiquement si les interfaces supposément internes sont également accessibles directement depuis l'extérieur, sans passer par le vecteur SSRF.
- Une API d'administration interne ne doit jamais être exposée sur une interface réseau publique, indépendamment de tout vecteur SSRF.
- L'authentification par simple jeton IAM sans contrôle de provenance (mTLS, VPC Endpoint, IP Restreinte) permet à n'importe quel attaquant possédant le jeton de s'authentifier à distance.
- En audit cloud : toujours tester l'accès direct aux routes internes, pas seulement via les vecteurs de pivotage identifiés.

---

## Analyse GRC - Recommandations

**La chaîne complète vue par OWASP :**

```
A10:2021 – Server-Side Request Forgery  → Endpoint récupérant des URLs fournies par l'utilisateur sans validation ni filtrage des destinations 
A01:2021 – Broken Access Control     → API d'administration exposée publiquement sur Internet
A01:2021 – Broken Access Control     → Politique IAM sur-permissionnée (accès aux secrets système)
A05:2021 – Security Misconfiguration → Utilisation d'IMDSv1
A09:2021 – Security Logging and Monitoring Failures → Absence de filtrage d'URL
A05:2021 – Security Misconfiguration → Présence de données/secrets dans les scripts user-data
```

---

La chaîne d'exploitation met en évidence plusieurs contrôles indépendants défaillants, qui repose plutôt sur l'accumulation de plusieurs faiblesses permettant la défense en profondeur de ne pas fonctionner.


| Risque | CWE | Impact | Recommandation |
|---|---|---|---|
| Pivotage et reconnaissance interne via SSRF| CWE-918 | Accès non autorisé aux services locaux et au service de métadonnées cloud | Implementer une allowlist d'URLs et bloquer l'accès aux plages d'IPs privées et link-local (169.254.0.0/16) |
| Vol d'identifiants temporaires IAM via IMDSv1 | CWE-1188 | Compromission des credentials du rôle de l'instance par simple requête HTTP GET | Forcer IMDSv2 (HttpTokens=required) et audits réguliers|
| API interne exposée | CWE-284 | Exécution de commandes d'administration depuis Internet à l'aide du jeton volé | Isoler l'API au niveau réseau (Security Group AWS restreignant l'accès au VPC, VPC Endpoint) et exiger un contrôle de provenance côté applicatif (mTLS ou restriction IP). L'authentification par jeton seul est insuffisante si l'interface est joignable depuis Internet |
| Rôle IAM trop permissif| CWE-250 | Escalade de privilèges et fuite de secrets critiques | Appliquer le moindre privilège sur le rôle EC2 et utiliser AWS Secrets Manager au lieu du user-data |
| Secrets en clair dans user-data | CWE-312 | Secret lisible via IMDS par tout processus local | Secrets Manager + Key Management Service, jamais dans user-data |
| Absence de détection| CWE-778 | Incapacité à identifier l'exfiltration de jetons et l'accès illégitime à l'API | Centraliser les logs (CloudTrail, VPC Flow Logs) et activer GuardDuty (Service AWS: Détection Intelligente des Menaces). En pratique : surveiller les appels STS (Security Token Service) AssumeRole hors-VPC, les accès IMDS anormaux (volume, source), et les requêtes sur `/control-plane/` depuis des IPs extérieures au VPC |


---

## Leçons Apprises

- L'IMDSv1 est une cible prioritaire : En environnement cloud, l'endpoint 169.254.169.254 doit systématiquement faire l'objet de mesures de durcissement spécifiques.
- Un service interne exposé est un service compromis : La sécurité ne doit jamais reposer sur la seule présence d'un pare-feu frontal ou d'un nom de domaine privé.
- Défense en profondeur : Si le SSRF avait été bloqué, le vol de jeton n'aurait pas eu lieu. Si IMDSv2 avait été exigé, le jeton n'aurait pas pu être extrait. Si l'API interne avait été isolée, le jeton n'aurait pas permis de lire les secrets.


---

## License

Original analysis © Solène Figueiredo.

Licensed under **CC BY 4.0**.

Challenge names and any third-party intellectual property remain the property of their respective owners.