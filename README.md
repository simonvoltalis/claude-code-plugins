<div align="center">

# 🧩 Claude Code — Plugins &amp; Skills

**Registre perso des plugins installés : ce qu'ils font, comment les invoquer, et pourquoi (ou pourquoi pas).**

![Status](https://img.shields.io/badge/statut-priv%C3%A9-6e5494?style=flat-square)
![Plugins](https://img.shields.io/badge/plugins%20actifs-3-2ea44f?style=flat-square)
![Écartés](https://img.shields.io/badge/écartés-1-e05d44?style=flat-square)
![Dernière mise à jour](https://img.shields.io/badge/maj-2026--09--28-blue?style=flat-square)

</div>

---

## 📋 Vue d'ensemble

| Plugin | Marketplace | Ce que ça fait | Invocation | Statut |
|---|---|---|---|---|
| 🎀 [**ponytail**](#-ponytail) | `DietrichGebert/ponytail` | Pousse vers la solution la plus simple (YAGNI, stdlib avant lib) | `/ponytail`, automatique sur tâche de code | ✅ Actif |
| 🕸️ [**graphify**](#️-graphify) | *(CLI + skill, hors marketplace)* | Graphe de connaissance du code — requêtes au lieu de grep/lecture brute | `/graphify .`, `graphify query "..."` | ✅ Actif |
| 🛠️ [**agent-skills**](#️-agent-skills) | `addyosmani/agent-skills` | Cycle de dev complet : spec → plan → build → test → review → ship | `/spec`, `/plan`, `/build`, `/review`, `/ship`... | ✅ Actif |
| 🚫 [**OmniRoute**](#-écarté--omniroute) | `diegosouzapw/OmniRoute` | Gateway IA multi-fournisseurs (routage vers 358 providers) | — | ❌ Écarté |

---

## 🎀 ponytail

> *"Force la solution la plus paresseuse qui marche vraiment — la plus simple, la plus courte, la plus minimale."*

**Marketplace :** `DietrichGebert/ponytail`
**Installation :**
```
/plugin marketplace add DietrichGebert/ponytail
/plugin install ponytail@ponytail
```

**Ce que ça fait :** avant d'écrire, refactorer ou choisir une dépendance, ponytail challenge la nécessité même de la tâche (YAGNI), privilégie la stdlib et les fonctionnalités natives avant tout package externe, et vise une ligne plutôt que cinquante.

**Comment l'utiliser :**

| Commande | Usage |
|---|---|
| `/ponytail` | Écrire/refactorer en mode minimal (niveaux `lite`, `full`, `ultra`) |
| `/ponytail-review` | Review d'un diff, focus uniquement sur la sur-ingénierie |
| `/ponytail-audit` | Audit de tout le repo — liste ce qui peut être supprimé |
| `/ponytail-debt` | Recense les raccourcis pris (commentaires `ponytail:`) |
| `/ponytail-gain` | Tableau de bord de l'impact mesuré (moins de code, moins de coût) |
| `/ponytail-help` | Mémo de toutes les commandes |

**En pratique :** pas besoin de le taper à chaque fois — dès qu'une tâche de code est en cours, l'assistant est censé l'appliquer automatiquement (c'est écrit dans le déclencheur du skill).

---

## 🕸️ graphify

> *Transforme n'importe quel code (et docs/PDF/images) en graphe de connaissance interrogeable — évite de faire lire des fichiers bruts à l'assistant.*

**Package :** [`graphifyy`](https://pypi.org/project/graphifyy/) sur PyPI (⚠️ deux "y" — le repo officiel est [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify))

**Installation** (nécessite Python 3.10+, `uv` recommandé) :
```bash
uv tool install graphifyy --python 3.12
graphify install --project      # scope projet, ou sans --project pour global
```

**Ce que ça fait :**
- Analyse le code **localement** avec tree-sitter (~40 langages), sans LLM ni clé API pour cette partie
- Détecte les "god nodes" (concepts les plus connectés), les communautés (sous-systèmes), les relations cross-fichiers (`calls`/`imports`/`inherits`)
- Installe des hooks `PreToolUse` **non bloquants** : suggère `graphify query` avant un grep/read classique, sans jamais bloquer un appel légitime (vérifié dans le code source)

**Comment l'utiliser :**

| Commande | Usage |
|---|---|
| `/graphify .` | Construit le graphe pour le projet courant |
| `graphify query "<question>"` | Interroge le graphe en langage naturel |
| `graphify path "<A>" "<B>"` | Trace le chemin entre deux concepts/fichiers |
| `graphify explain "<concept>"` | Explication ciblée d'un concept |
| `graphify update .` | Met à jour le graphe après une modification de code |

**Où c'est installé :** `.claude/skills/graphify/` (scope projet) + hooks dans `.claude/settings.json`.

---

## 🛠️ agent-skills

> *Suite de skills "production-grade" couvrant tout le cycle de vie d'une feature — de la spec au ship.*

**Marketplace :** `addyosmani/agent-skills`
**Installation :**
```
/plugin marketplace add https://github.com/addyosmani/agent-skills.git
/plugin install agent-skills@addy-agent-skills
```
> ⚠️ Nécessite une clé SSH enregistrée sur GitHub — l'installateur de plugin clone en SSH même quand la marketplace a été ajoutée en HTTPS (limitation connue de Claude Code, pas du plugin). Voir [🔑 Prérequis SSH](#-prérequis-ssh) plus bas si `Host key verification failed` apparaît.

**Ce que ça fait :** ajoute des skills et 4 agents spécialisés pour chaque étape d'un projet.

<details>
<summary><b>Agents ajoutés</b></summary>

| Agent | Rôle |
|---|---|
| `code-reviewer` | Review sur 5 axes : correction, lisibilité, architecture, sécurité, performance |
| `security-auditor` | Détection de vulnérabilités, threat modeling |
| `test-engineer` | Stratégie de tests, écriture, analyse de couverture |
| `web-performance-auditor` | Core Web Vitals, chargement, rendu, réseau |

</details>

<details>
<summary><b>Skills principaux (cycle de dev)</b></summary>

| Commande | Étape | Ce que ça fait |
|---|---|---|
| `/spec` | Spécifier | Écrit une spec structurée avant le code |
| `/plan` | Planifier | Découpe en tâches vérifiables, ordonnées par dépendance |
| `/build` | Construire | Implémente par petites étapes : build → test → verify → commit |
| `/test` | Tester | Workflow TDD (tests d'abord), ou pattern "Prove-It" pour un bug |
| `/review` | Reviewer | Review 5 axes avant merge |
| `/ship` | Livrer | Checklist de pré-lancement via plusieurs personas en parallèle |
| `/code-simplify` | Nettoyer | Simplifie sans changer le comportement |
| `/constraints` | Cadrer | Définit et fait respecter le niveau de qualité du projet (`CONSTRAINTS.md`) |
| `/webperf` | Performance | Audit web via le persona performance |

</details>

<details>
<summary><b>Autres skills disponibles (à la demande)</b></summary>

`api-and-interface-design` · `browser-testing-with-devtools` · `ci-cd-and-automation` · `code-review-and-quality` · `code-simplification` · `constraint-driven-development` · `context-engineering` · `debugging-and-error-recovery` · `deprecation-and-migration` · `documentation-and-adrs` · `doubt-driven-development` · `frontend-ui-engineering` · `git-workflow-and-versioning` · `idea-refine` · `incremental-implementation` · `interview-me` · `observability-and-instrumentation` · `performance-optimization` · `planning-and-task-breakdown` · `security-and-hardening` · `shipping-and-launch` · `source-driven-development` · `spec-driven-development` · `test-driven-development`

</details>

---

## 🚫 Écarté — OmniRoute

**Repo :** [`diegosouzapw/OmniRoute`](https://github.com/diegosouzapw/OmniRoute) — gateway IA gratuite (358 fournisseurs, fallback automatique, compression de tokens)

**Verdict : légitime, mais écarté pour usage pro.**

| Vérification | Résultat |
|---|---|
| Compte GitHub | Réel, actif depuis 2014, 2300+ followers — pas un compte jetable |
| Hygiène du repo | Scan de secrets, scanner de vulnérabilités, linter de sécurité CI, `SECURITY.md` |
| Historique | Commits cohérents, vraie revue de PR, CVE publiée et corrigée récemment |
| **Risque retenu** | Route le code/les prompts vers des dizaines de fournisseurs tiers non vérifiés par l'entreprise, même en auto-hébergé |

**Règle retenue :** légitimité du projet et acceptabilité pour router des données pro sont **deux questions séparées**. Un outil peut être parfaitement fiable techniquement et rester à éviter dès qu'il change *où* le code/les prompts sont envoyés, sans revue de gouvernance des données au préalable.

---

## 🔑 Prérequis SSH

Certains plugins (`source: "github"` dans leur `marketplace.json`) sont clonés en SSH par Claude Code, quelle que soit la méthode utilisée pour ajouter la marketplace. Si tu vois `Host key verification failed` ou `Permission denied (publickey)` :

```bash
# 1. Ajouter la clé d'hôte GitHub (vérifiée : SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU)
ssh-keyscan -t ed25519 github.com >> ~/.ssh/known_hosts

# 2. Générer une clé si aucune n'existe
ssh-keygen -t ed25519 -C "<toi>@users.noreply.github.com" -f ~/.ssh/id_ed25519

# 3. Ajouter la clé publique sur github.com/settings/ssh/new, puis vérifier :
ssh -T git@github.com
```

---

## ➕ Ajouter un plugin à ce registre

1. `/plugin marketplace add <owner>/<repo>` puis `/plugin install <plugin>@<marketplace>`
2. Vérifier la légitimité si le plugin touche à des données sensibles (voir grille OmniRoute ci-dessus)
3. Ajouter une section ici : ce que ça fait, comment l'invoquer, où c'est installé
