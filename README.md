# Audit de sécurité — OWASP Top 10:2025 sur OWASP Juice Shop

> ⚠️ **Cadre pédagogique uniquement.** Tous les tests présentés dans ce dépôt ont été réalisés sur [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/), une application officiellement conçue par l'OWASP pour être volontairement vulnérable et servir de support d'entraînement. Aucune des techniques documentées ici n'a été, ou ne doit être, appliquée à une application tierce sans autorisation explicite.

## Contexte

Ce dépôt documente un audit de sécurité applicative mené selon le référentiel **OWASP Top 10:2025**, dans le cadre d'un stage orienté cybersécurité. La démarche a suivi une progression pédagogique : acquisition des fondamentaux de la sécurité offensive, étude approfondie du classement OWASP Top 10, entraînement pratique par challenges CTF (TryHackMe, picoCTF), puis conduite d'un audit structuré sur une application d'entraînement représentative.

## Méthodologie

- **Approche** : boîte grise, inspirée de l'OWASP Testing Guide (WSTG)
- **Phases** : reconnaissance → identification de la vulnérabilité → exploitation / preuve de concept → documentation systématique
- **Format de documentation** : chaque test suit une fiche standardisée en 6 blocs — *Description générale, Faille spécifique, Étapes de reproduction, Preuve de concept, Impact, Remédiation*
- **Grille de sévérité** : Critique / Élevée / Moyenne / Faible, évaluée selon l'impact combiné sur la Confidentialité, l'Intégrité et la Disponibilité, et la facilité d'exploitation

## Environnement de test

| | |
|---|---|
| Application cible | OWASP Juice Shop (`bkimminich/juice-shop:latest`, via Docker) |
| Machine d'attaque | Kali Linux 2025.3 (VMware) |
| Outils | Burp Suite Community Edition, Firefox DevTools, dirb, Snyk CLI, jwt.io Debugger, PostBin |

## Résultats — 20 tests, 10 catégories couvertes

| ID | Catégorie | Test | Sévérité |
|---|---|---|---|
| [T-A01-01](findings/A01-broken-access-control/T-A01-01.md) | A01 | Bypass admin par injection SQL | Critique |
| [T-A01-02](findings/A01-broken-access-control/T-A01-02.md) | A01 | Forged Review (IDOR) | Élevée |
| [T-A01-03](findings/A01-broken-access-control/T-A01-03.md) | A01 | SSRF via la photo de profil | Critique |
| [T-A02-01](findings/A02-security-misconfiguration/T-A02-01.md) | A02 | Fichier de sauvegarde exposé (Forgotten Developer Backup) | Élevée |
| [T-A03-01](findings/A03-software-supply-chain/T-A03-01.md) | A03 | Typosquatting d'un paquet npm | Moyenne |
| [T-A03-02](findings/A03-software-supply-chain/T-A03-02.md) | A03 | Dépendance obsolète vulnérable (Snyk) | Élevée |
| [T-A04-01](findings/A04-cryptographic-failures/T-A04-01.md) | A04 | JWT non signé (`alg: none`) | Critique |
| [T-A05-01](findings/A05-injection/T-A05-01.md) | A05 | Injection SQL — bypass d'authentification ciblé | Critique |
| [T-A05-02](findings/A05-injection/T-A05-02.md) | A05 | Injection SQL UNION-based — extraction de données | Critique |
| [T-A05-03](findings/A05-injection/T-A05-03.md) | A05 | XSS réfléchi | Moyenne |
| [T-A05-04](findings/A05-injection/T-A05-04.md) | A05 | Injection NoSQL (opérateur `$ne`) | Critique |
| [T-A06-01](findings/A06-insecure-design/T-A06-01.md) | A06 | Changement de mot de passe sans vérification | Moyenne |
| [T-A06-02](findings/A06-insecure-design/T-A06-02.md) | A06 | Contournement de paiement par manipulation du DOM | Moyenne |
| [T-A06-03](findings/A06-insecure-design/T-A06-03.md) | A06 | Fraude financière par quantité négative | Élevée |
| [T-A07-01](findings/A07-authentication-failures/T-A07-01.md) | A07 | Mot de passe admin trouvé par force brute | Critique |
| [T-A07-02](findings/A07-authentication-failures/T-A07-02.md) | A07 | Reset de mot de passe via question de sécurité | Critique |
| [T-A08-01](findings/A08-software-data-integrity/T-A08-01.md) | A08 | Élévation de privilèges par mass assignment | Critique |
| [T-A09-01](findings/A09-logging-alerting-failures/T-A09-01.md) | A09 | Endpoint de métriques exposé publiquement | Élevée |
| [T-A09-02](findings/A09-logging-alerting-failures/T-A09-02.md) | A09 | Répertoire de logs exposé publiquement | Élevée |
| [T-A10-01](findings/A10-exceptional-conditions/T-A10-01.md) | A10 | Message d'erreur exposant la stack trace | Moyenne |

**Répartition par sévérité :** 8 Critique · 6 Élevée · 6 Moyenne · 0 Faible

## Constats transverses

- **Trois chemins indépendants** mènent à un accès administrateur (injection SQL, force brute, mass assignment) — illustre l'importance d'une défense en profondeur plutôt que de contrôles isolés.
- **Absence récurrente de limitation des tentatives** (rate limiting) sur l'authentification, la réinitialisation de mot de passe, et confirmée par l'absence d'alerte dans les journaux d'accès eux-mêmes exposés.
- **Chaîne d'exploitation combinée** : un endpoint de métriques internes découvert exposé (T-A09-01) reste accessible via une vulnérabilité SSRF (T-A01-03) même en cas de restriction réseau — une faille mineure prise isolément voit son impact amplifié par une seconde faille distincte.

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
LICENSE
```

## Licence

Ce travail est publié sous licence [MIT](LICENSE) — libre de réutilisation et de citation.
