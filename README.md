# Audit de sécurité — OWASP Top 10:2025 sur OWASP Juice Shop

> ⚠️ **Cadre pédagogique uniquement.** Tous les tests présentés dans ce dépôt ont été réalisés sur [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/), une application officiellement conçue par l'OWASP pour être volontairement vulnérable et servir de support d'entraînement. Aucune des techniques documentées ici n'a été, ou ne doit être, appliquée à une application tierce sans autorisation explicite.

## Contexte

Ce dépôt documente un audit de sécurité applicative mené selon le référentiel **OWASP Top 10:2025**, dans le cadre d'un stage orienté cybersécurité. La démarche a suivi une progression pédagogique : acquisition des fondamentaux de la sécurité offensive, étude approfondie du classement OWASP Top 10, entraînement pratique par challenges CTF (TryHackMe, picoCTF), puis conduite d'un audit structuré sur une application d'entraînement représentative.

## Méthodologie

- **Approche** : boîte grise, inspirée de l'OWASP Testing Guide (WSTG)
- **Phases** : reconnaissance → identification de la vulnérabilité → exploitation / preuve de concept → documentation systématique
- **Format de documentation** : chaque test suit une fiche standardisée en 6 blocs — *Description générale, Faille spécifique, Étapes de reproduction, Preuve de concept, Impact, Remédiation*
- **Grille de sévérité** : appréciation qualitative préliminaire (Critique / Élevée / Moyenne / Faible), fondée sur l'impact observé et la facilité d'exploitation. Il ne s'agit pas d'un calcul CVSS : les notes doivent être lues avec les limites précisées dans chaque fiche.

## Environnement de test

| | |
|---|---|
| Application cible | OWASP Juice Shop (Docker ; tag `latest` utilisé lors du test, révision exacte non consignée) |
| Machine d'attaque | Kali Linux 2025.3 (VMware) |
| Outils | Burp Suite Community Edition, Firefox DevTools, dirb, Snyk CLI, jwt.io Debugger, PostBin |

## Résultats — 20 tests documentés, 10 catégories couvertes

> Les 20 fiches ne correspondent pas toutes à des vulnérabilités confirmées distinctes : certaines documentent une tentative bloquée, un risque illustratif ou une chaîne d'exploitation déjà décrite ailleurs.

| ID | Catégorie | Test | Sévérité |
|---|---|---|---|
| [T-A01-01](findings/A01-broken-access-control/T-A01-01.md) | A05 (cause) / A01 (impact) | Accès admin après injection SQL — chaîne documentée avec T-A05-01, non comptée comme une faille distincte | Critique |
| [T-A01-02](findings/A01-broken-access-control/T-A01-02.md) | A01 | Forged Review (IDOR) | Élevée |
| [T-A01-03](findings/A01-broken-access-control/T-A01-03.md) | A01 | SSRF via la photo de profil — requête serveur externe et localhost confirmées | Élevée |
| [T-A02-01](findings/A02-security-misconfiguration/T-A02-01.md) | A02 | Fichier de sauvegarde exposé (Forgotten Developer Backup) | Élevée |
| [T-A03-01](findings/A03-software-supply-chain/T-A03-01.md) | A03 | Paquet de démonstration repéré dans une sauvegarde — risque illustratif, installation/exécution non démontrée | Information |
| [T-A03-02](findings/A03-software-supply-chain/T-A03-02.md) | A03 | Dépendance obsolète vulnérable (Snyk) | Élevée |
| [T-A04-01](findings/A04-cryptographic-failures/T-A04-01.md) | A04 | JWT non signé (`alg: none`) | Critique |
| [T-A05-01](findings/A05-injection/T-A05-01.md) | A05 | Injection SQL — bypass d'authentification ciblé | Critique |
| [T-A05-02](findings/A05-injection/T-A05-02.md) | A05 | Injection SQL UNION-based — extraction de données | Critique |
| [T-A05-03](findings/A05-injection/T-A05-03.md) | A05 | XSS réfléchi | Moyenne |
| [T-A05-04](findings/A05-injection/T-A05-04.md) | A05 | Injection NoSQL (opérateur `$ne`) | Critique |
| [T-A06-01](findings/A06-insecure-design/T-A06-01.md) | A06 | Changement de mot de passe sans vérification | Moyenne |
| [T-A06-02](findings/A06-insecure-design/T-A06-02.md) | A06 | Tentative de déblocage du bouton — refusée côté serveur, aucune faille de paiement confirmée | Non confirmé |
| [T-A06-03](findings/A06-insecure-design/T-A06-03.md) | A06 | Fraude financière par quantité négative | Élevée |
| [T-A07-01](findings/A07-authentication-failures/T-A07-01.md) | A07 | Mot de passe admin trouvé par force brute | Critique |
| [T-A07-02](findings/A07-authentication-failures/T-A07-02.md) | A07 | Reset de mot de passe via question de sécurité | Critique |
| [T-A08-01](findings/A08-software-data-integrity/T-A08-01.md) | A08 | Élévation de privilèges par mass assignment | Critique |
| [T-A09-01](findings/A09-logging-alerting-failures/T-A09-01.md) | A02 (principal) / A09 (secondaire à justifier) | Endpoint de métriques exposé publiquement | Élevée |
| [T-A09-02](findings/A09-logging-alerting-failures/T-A09-02.md) | A02 (principal) / A01 (exposition de données) | Répertoire de journaux exposé publiquement | Élevée |
| [T-A10-01](findings/A10-exceptional-conditions/T-A10-01.md) | A10 | Message d'erreur exposant la stack trace | Moyenne |

Les sévérités sont des estimations qualitatives et non des scores CVSS. T-A03-01 est un constat illustratif et T-A06-02 n'a pas confirmé de vulnérabilité. T-A01-01 et T-A05-01 documentent deux aspects de la même injection SQL et ne doivent pas être additionnés comme deux failles indépendantes.

## Limites de reproductibilité

L'environnement d'origine utilisait l'image Docker `latest`. Comme ce tag évolue, les constats peuvent ne plus être reproductibles à l'identique. La version exacte de l'image, son digest, la révision de Juice Shop et la date d'exécution doivent être ajoutés avant de présenter les résultats comme reproductibles. Les analyses de dépendances et de CVE doivent également inclure la date, la base de données interrogée et la sortie complète de l'outil.

## Constats transverses

- Les comptes de sévérité ne sont pas fournis tant qu'un calcul cohérent n'est pas documenté. Les catégories indiquées dans le tableau donnent la cause principale, puis les catégories secondaires quand elles éclairent l'impact.
- L'absence de réponse HTTP 429 ou de verrouillage dans une séquence observée ne prouve pas, à elle seule, l'absence générale de limitation de débit. Les conclusions des fiches A07 doivent rester limitées aux scénarios effectivement testés.
- La présence de /metrics dans les métriques de l'application ne prouve pas que l'endpoint serait privé dans un autre déploiement ; le constat porte sur l'instance Juice Shop observée.

- **Trois chemins indépendants** mènent à un accès administrateur (injection SQL, force brute, mass assignment) — illustre l'importance d'une défense en profondeur plutôt que de contrôles isolés.
- Plusieurs tests portent sur la limitation des tentatives de connexion et de réinitialisation. Les résultats doivent être présentés endpoint par endpoint, avec le nombre d'essais, leur cadence, les réponses et les éventuels mécanismes de blocage observés.
- **Chaîne d'exploitation observée** : la fonctionnalité SSRF a déclenché une requête vers `/metrics` sur localhost. Comme cet endpoint était déjà directement accessible dans l'instance, le test ne démontre pas un contournement de pare-feu ou d'une restriction réseau.

## Structure du dépôt

```
findings/
  A01-broken-access-control/
  A02-security-misconfiguration/
  A03-software-supply-chain/
  A04-cryptographic-failures/
  A05-injection/
  A06-insecure-design/
  A07-authentication-failures/
  A08-software-data-integrity/
  A09-logging-alerting-failures/
  A10-exceptional-conditions/
screenshots/
README.md
(LICENSE à ajouter)
```

## Licence

Aucune licence n'est fournie dans cette copie du dépôt. Ajoutez la licence choisie et le titulaire exact des droits avant d'annoncer des conditions de réutilisation.
