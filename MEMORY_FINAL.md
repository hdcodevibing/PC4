# MEMORY — Pipeline Philologique Rap Français
# Repo : hdcodevibing/PC4 (GitHub)
# Dernière mise à jour : 2026-04-10

---

## Contexte du projet

Projet de recherche philologique sur le rap français contemporain. Construction d'une base de désambiguïsation lexicale et d'annotation thématique inter-corpus, destinée à alimenter un générateur de lyrics par combinaison d'hémistiches.

**Opérateur** : H — expert rap français, autorité sur toutes les questions de sens.
**Stack** : Claude Code (GitHub / web) — tout se passe ici, dans cette interface.
**Modèle pipeline** : `claude-haiku-4-5-20251001` — toutes les étapes automatisées.
**Repo** : `hdcodevibing/PC4` · Branche de travail : `claude/pipeline-start-JOK7a`

---

## Principe fondamental

**Tout se passe ici.** Plus de passage par claude.ai.

H reste l'autorité absolue sur le sens — mais la désambiguïsation se fait directement dans Claude Code. Claude Code présente les termes du delta, H valide/corrige/arbitre, et on avance sans changer d'interface.

---

## Flux complet (version GitHub)

```
H uploade corpus_brut.md dans etude/ (via GitHub ou dépôt direct)
        ↓
pipeline start
        ↓
[HAIKU] scan corpus vs lexique_master → delta-[slug].md
        ↓
[ICI] Claude Code présente chaque terme du delta à H
H désambiguïse / valide / arbitre directement dans la session
Claude Code génère delta-[slug]-fix.md
        ↓
pipeline resume
        ↓
[HAIKU] fusion delta-fix → lexique_master.md mis à jour
[HAIKU] annotation exponentielle → corpus annoté
[HAIKU] cartographie thématique
[HAIKU] annotation thématique
[HAIKU] assemblage fichier unifié + synthèse
        ↓
output/CORPUS-[NNN]-[ARTISTE]-[PROJET]/
  ├── fichier_unifie.md
  └── synthese.md
        ↓
[GIT] commit + push sur branche de travail
```

---

## Désambiguïsation en session Claude Code

Quand le delta est prêt, Claude Code parcourt chaque terme et demande à H de statuer. Format de travail :

```
Terme : [terme]
Vers  : [vers complet]
→ Options : [proposition A] / [proposition B] / autre ?
```

H répond directement. Claude Code enregistre. Quand tous les termes sont traités, Claude Code génère automatiquement `delta-[slug]-fix.md` et continue le pipeline.

Règle absolue : Claude Code ne désambiguïse jamais seul — H a toujours le dernier mot.

---

## Conventions de nommage — exactes et non négociables

| Fichier | Emplacement | Créé par |
|---------|-------------|----------|
| `corpus_brut.md` | `etude/` | H (upload GitHub) |
| `lexique_master.md` | `etude/` | Claude Code (auto) |
| `delta-[artiste]-[projet].md` | `etude/` | Claude Code |
| `delta-[artiste]-[projet]-fix.md` | `etude/` | Claude Code (après session désambiguïsation avec H) |
| `registre_taxonomique_master.md` | `registre/` | Claude Code + H |
| `lexique_master_v[N].md` | `archives/` | H (sauvegardes) |

**Tirets** dans les noms de fichiers delta — jamais de tirets bas.

---

## Architecture GitHub

```
hdcodevibing/PC4/
├── CLAUDE.md                        ← instructions pipeline
├── MEMORY_FINAL.md                  ← ce fichier
├── pipeline_state.json              ← état courant
├── etude/
│   ├── corpus_brut.md
│   ├── lexique_master.md
│   ├── delta-[slug].md
│   └── delta-[slug]-fix.md
├── output/
│   └── CORPUS-[NNN]-[ARTISTE]-[PROJET]/
│       ├── fichier_unifie.md
│       └── synthese.md
├── registre/
└── archives/
```

Tout commit et push se fait sur la branche de travail désignée. La branche `main` ne reçoit que les livrables finaux validés par H.

---

## État des corpus traités

| Code-barre | Artiste / Projet | Date | Termes ajoutés |
|------------|-----------------|------|----------------|
| CORPUS-003-DJADJA_DINAZ-SPLEEN | DJADJA & DINAZ / SPLEEN | 2026-04-01 | 14 |

*Mis à jour automatiquement après chaque pipeline finish.*

---

## Corpus en cours

| Artiste / Projet | Slug | Étape | Date début |
|-----------------|------|-------|------------|
| SCH / JULIUS | sch-julius | Scan terminé — désambiguïsation à faire ici | 2026-04-10 |

---

## Décisions architecturales

| Décision | Raison |
|----------|--------|
| Tout dans Claude Code GitHub | Interface unique — plus de fragmentation entre outils |
| Désambiguïsation en session | H reste l'autorité sans changer d'interface |
| Haiku pour les étapes automatisées | Tâches longues et structurées — Haiku suffit et coûte moins |
| Zéro validation intermédiaire | Corrections en aval au moment de la génération |
| Vers complets dans le delta | Conserver le contexte pour la désambiguïsation de H |
| temperature: 0 | Tâches répétitives — stabilité prioritaire |
| Prompt caching sur lexique + corpus | Coût quasi nul après le premier appel |
| Git commit après chaque étape | Traçabilité complète — rien ne se perd |

---

## Ce que Claude Code ne fait jamais

- Désambiguïser un terme sans l'avis de H
- Passer à l'étape suivante si un fichier attendu est absent ou mal nommé
- Modifier les fichiers déposés par H
- Supprimer `lexique_master.md`, `output/`, `registre/` ou `archives/`
- Utiliser un autre modèle que `claude-haiku-4-5-20251001` pour les étapes automatisées
- Pusher sur `main` sans validation explicite de H
