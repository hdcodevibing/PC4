# MEMORY — Pipeline Philologique Rap Français
# /Users/macbookpro/Desktop/PC4/
# Dernière mise à jour : mars 2026

---

## Contexte du projet

Projet de recherche philologique sur le rap français contemporain. Construction d'une base de désambiguïsation lexicale et d'annotation thématique inter-corpus, destinée à alimenter un générateur de lyrics par combinaison d'hémistiches.

**Opérateur** : H — expert rap français, autorité sur toutes les questions de sens.
**Stack** : Claude Code terminal (Mac) + claude.ai + API Anthropic.
**Modèle pipeline** : `claude-haiku-4-5-20251001` — toutes les étapes automatisées.
**Dossier de travail** : `/Users/macbookpro/Desktop/PC4/`

---

## Principe fondamental

Un seul moment humain par corpus : la désambiguïsation du delta dans claude.ai.
Tout le reste tourne de manière autonome via Haiku API.

---

## Flux complet

```
H dépose corpus_brut.md dans PC4/etude/
        ↓
pipeline start
        ↓
[HAIKU] scan corpus vs lexique_master → delta-[slug].md
        ↓
H → claude.ai Projet "Pipeline 3-en-1" → mode désambiguïsation
H dépose delta-[slug]-fix.md dans PC4/etude/
        ↓
pipeline resume
        ↓
[HAIKU] fusion delta-fix → lexique_master.md mis à jour
[HAIKU] annotation exponentielle → corpus annoté
[HAIKU] cartographie thématique
[HAIKU] annotation thématique
[HAIKU] assemblage fichier unifié + synthèse
        ↓
PC4/output/CORPUS-[NNN]-[ARTISTE]-[PROJET]/
  ├── fichier_unifie.md
  └── synthese.md
```

---

## Conventions de nommage — exactes et non négociables

| Fichier | Emplacement | Créé par |
|---------|-------------|----------|
| `corpus_brut.md` | `etude/` | H |
| `lexique_master.md` | `etude/` | Claude Code (auto) |
| `delta-[artiste]-[projet].md` | `etude/` | Claude Code |
| `delta-[artiste]-[projet]-fix.md` | `etude/` | H après claude.ai |
| `registre_taxonomique_master.md` | `registre/` | H + Projet Registre |
| `lexique_master_v[N].md` | `archives/` | H (sauvegardes manuelles) |

**Tirets** dans les noms de fichiers delta — jamais de tirets bas.

---

## Projets claude.ai associés

| Projet | Rôle |
|--------|------|
| Pipeline 3-en-1 | Désambiguïsation delta uniquement |
| Registre Taxonomique | Enrichissement taxonomie après chaque corpus |
| Générateur | Composition lyrics multi-corpus |

---

## État des corpus traités

| Code-barre | Artiste / Projet | Date | Termes ajoutés |
|------------|-----------------|------|----------------|
| CORPUS-003-DJADJA_DINAZ-SPLEEN | DJADJA & DINAZ / SPLEEN | 2026-04-01 | 14 |

*Mis à jour automatiquement après chaque pipeline finish.*

---

## Décisions architecturales

| Décision | Raison |
|----------|--------|
| Haiku pour tout le pipeline | Tâches longues et structurées — Haiku suffit |
| Zéro validation intermédiaire | Cartographie et annotation robustes sur corpus propre — corrections en aval au moment de la génération |
| Delta uniquement dans claude.ai | Réduire le travail humain au strict nécessaire |
| Vers complets dans le delta | Conserver le contexte pour la désambiguïsation |
| temperature: 0 | Tâches répétitives — stabilité prioritaire |
| Prompt caching sur lexique + corpus | Coût quasi nul après le premier appel |
| Corrections au moment de la génération | Plus efficace que valider dans le vide à chaque corpus |
