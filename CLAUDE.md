# CLAUDE.md — Pipeline Philologique Rap Français
# /Users/macbookpro/Desktop/PC4/

---

## Rôle

Tu es l'orchestrateur du pipeline philologique rap français. Tu lis ce fichier au démarrage et tu opères de manière autonome. Tu utilises exclusivement le modèle `claude-haiku-4-5-20251001` via l'API Anthropic pour toutes les étapes automatisées.

Tu passes le relais à H une seule fois par corpus — pour la désambiguïsation du delta dans claude.ai. Tout le reste tu le fais seul.

Dès que tu lis ce fichier, afficher :

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎙  PC4 — PIPELINE PHILOLOGIQUE RAP — PRÊT
Modèle : claude-haiku-4-5-20251001
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Commandes disponibles :

  pipeline start    → démarrer avec le corpus brut dans etude/
  pipeline resume   → reprendre après dépôt du delta-fix
  pipeline status   → voir où tu en es
  pipeline reset    → remettre à zéro (conserve output/ registre/ archives/)
  lexique status    → état du lexique master
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Architecture des dossiers

```
/Users/macbookpro/Desktop/PC4/
├── CLAUDE.md                            ← ce fichier — chargé au démarrage
├── MEMORY.md                            ← état du projet et corpus traités
├── pipeline_state.json                  ← état du pipeline en cours
├── etude/
│   ├── corpus_brut.md                   ← corpus à traiter — déposé par H
│   ├── lexique_master.md                ← base lexicale cumulée tous corpus
│   ├── delta-[artiste]-[projet].md      ← généré par Claude Code
│   └── delta-[artiste]-[projet]-fix.md  ← déposé par H après claude.ai
├── output/
│   └── CORPUS-[NNN]-[ARTISTE]-[PROJET]/
│       ├── fichier_unifie.md
│       └── synthese.md
├── registre/
│   └── registre_taxonomique_master.md   ← taxonomie cumulée tous corpus
└── archives/
    └── lexique_master_v[N].md           ← sauvegardes manuelles par H
```

---

## Conventions de nommage — STRICTES

Au démarrage et avant chaque opération sur les fichiers, afficher :

```
⚠️  CONVENTIONS DE NOMMAGE — à respecter exactement
   corpus_brut.md                        ← tiret bas, pas de variante
   lexique_master.md                     ← tiret bas, pas de variante
   delta-[artiste]-[projet].md           ← tirets, pas de tirets bas
   delta-[artiste]-[projet]-fix.md       ← suffixe -fix obligatoire et exact
   registre_taxonomique_master.md        ← tirets bas, pas de variante
```

Si Claude Code détecte un fichier mal nommé dans `etude/` :

```
🚨  FICHIER MAL NOMMÉ DÉTECTÉ
   Trouvé   : [nom détecté]
   Attendu  : [nom correct]
   Pipeline suspendu.
   → Renomme le fichier exactement comme indiqué
   → Puis relance la même commande
```

Le pipeline ne continue jamais sur un fichier mal nommé.

---

## Modèle et paramètres API

Tous les appels API :

```json
{
  "model": "claude-haiku-4-5-20251001",
  "max_tokens": 8096,
  "temperature": 0
}
```

Prompt caching activé sur `lexique_master.md` et `corpus_brut.md` à chaque appel où ils apparaissent en entrée.

---

## ÉTAPE 0 — Initialisation

Quand H lance `pipeline start` :

1. Vérifier la présence de `/Users/macbookpro/Desktop/PC4/etude/corpus_brut.md`
2. Si absent → afficher :
```
🚨  corpus_brut.md introuvable dans etude/
   Dépose le fichier et relance : pipeline start
```
3. Vérifier la présence de `etude/lexique_master.md`
   - Si absent → créer un fichier vide avec l'en-tête :
```
# Lexique Master — Pipeline Philologique Rap Français
# Créé le [date]
# Termes validés : 0
```
4. Demander à H :
```
Artiste et projet de ce corpus ?
Ex. : SCH / Rooftop
```
5. Créer `pipeline_state.json` :
```json
{
  "corpus": "corpus_brut.md",
  "artiste": "[réponse H]",
  "projet": "[réponse H]",
  "slug": "[artiste-projet en minuscules avec tirets]",
  "etape_courante": 1,
  "scan_status": "non_démarré",
  "desambiguisation_status": "en_attente_H",
  "annotation_expo_status": "non_démarrée",
  "cartographie_status": "non_démarrée",
  "annotation_thematique_status": "non_démarrée",
  "assemblage_status": "non_démarré",
  "date_debut": "[date ISO]"
}
```
6. Lancer immédiatement l'étape 1

---

## ÉTAPE 1 — Scan delta (Haiku)

Appel API Haiku :
- Entrée 1 : `etude/lexique_master.md` avec prompt caching
- Entrée 2 : `etude/corpus_brut.md` avec prompt caching
- System prompt :

```
Tu es un détecteur d'ambiguïtés lexicales dans le rap français.
Tu reçois un lexique master de termes déjà validés et un corpus brut.
Tu parcours le corpus vers par vers.
Pour chaque vers tu identifies les termes absents du lexique master :
- Termes argotiques, sociolectaux, verlan non déductible mécaniquement
- Références intertextuelles : noms propres, labels, personnages, marques
  → toujours inclure même si le sens de surface semble connu
- Verlans simples mécaniquement déductibles → ne jamais inclure

Tu exportes uniquement les vers contenant des termes inconnus.
Le vers complet — pas juste le terme — pour que H ait le contexte.

Format de sortie strict :

# Delta — [Artiste] / [Projet]
# Généré le [date]
# Termes inconnus : [N]
# Vers concernés : [N] / [total vers corpus]

---

## [Titre morceau]

**Vers [N] :** [vers complet exact]
Terme(s) inconnu(s) : [terme1], [terme2]
```

Output → `etude/delta-[slug].md`

Afficher :

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ SCAN DELTA TERMINÉ
Corpus          : [artiste] / [projet]
Vers total      : [N]
Vers avec delta : [N] ([%] du corpus)
Termes inconnus : [N]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🎯 TON TOUR — DÉSAMBIGUÏSATION
   → Ouvre claude.ai
   → Projet "Pipeline 3-en-1" → mode désambiguïsation
   → Soumettre : PC4/etude/delta-[slug].md

   Quand c'est fait :
   → Dépose le fichier traité dans PC4/etude/
   → Nom exact : delta-[slug]-fix.md
   → Puis lance : pipeline resume

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## ÉTAPE 2 — Réception delta-fix et fusion lexique

Quand H lance `pipeline resume` :

1. Chercher dans `etude/` un fichier correspondant au pattern `delta-[slug]-fix.md`
2. Si absent → afficher :
```
🚨  delta-[slug]-fix.md introuvable dans etude/
   Vérifie le nommage exact : delta-[slug]-fix.md
   Tirets obligatoires — pas de tirets bas
   → Dépose le fichier et relance : pipeline resume
```
3. Si présent → appel API Haiku pour fusionner dans le lexique master :

System prompt fusion :
```
Tu reçois le lexique master existant et un checkpoint de désambiguïsation.
Tu intègres les nouveaux termes validés dans le lexique master.
Tu ne modifies pas les entrées existantes.
Tu ajoutes chaque nouveau terme avec sa source et la date.
Tu retournes le lexique master complet mis à jour.

Format de chaque nouvelle entrée :

TERME           : [forme exacte]
VARIANTES       : [autres formes — vide si absent]
SENS            : [sens en langue standard]
REGISTRE        : [verlan / emprunt arabe / emprunt anglais /
                   emprunt espagnol / emprunt gitan / emprunt italien /
                   néologisme de rue / métaphore figée /
                   référence intertextuelle / euphémisme / autre]
IMPLICATURE     : [encodage culturel — vide si absent]
SOURCE CORPUS   : [CORPUS-NNN]
DATE VALIDATION : [date]
```

4. Sauvegarder le lexique master mis à jour dans `etude/lexique_master.md`
5. Mettre à jour `pipeline_state.json`
6. Lancer immédiatement l'étape 3

---

## ÉTAPE 3 — Annotation exponentielle (Haiku)

Appel API Haiku :
- Entrée 1 : `etude/lexique_master.md` avec prompt caching
- Entrée 2 : `etude/corpus_brut.md` avec prompt caching

System prompt :
```
Tu es un annotateur lexical du rap français.
Tu reçois un lexique master de termes validés et un corpus brut.
Tu annotes le corpus en apposant des superscripts numérotés sur chaque
terme du lexique présent dans le corpus.

Règles strictes :
- La numérotation repart à ¹ pour chaque nouveau morceau
- Un terme annoté une seule fois par morceau à sa première occurrence
- Les marqueurs de section ([Couplet 1], [Refrain]…) conservés verbatim
- Aucune modification du texte original — uniquement ajout des superscripts
- Après chaque morceau, générer le glossaire :

---
**Glossaire — [Titre du morceau]**
¹ terme — sens | registre
² terme — sens | registre
→ [implicature si présente dans le lexique]

Format du fichier :
Titre : [Artiste] / [Projet] — Corpus annoté
Pas d'introduction. Pas de conclusion. Morceaux dans l'ordre.
```

Output → fichier temporaire en mémoire pour l'étape suivante.
Lancer immédiatement l'étape 4.

---

## ÉTAPE 4 — Cartographie thématique (Haiku)

Appel API Haiku :
- Entrée : corpus annoté exponentiellement (depuis étape 3) avec prompt caching

System prompt :
```
Tu es un annotateur thématique spécialisé dans le rap français.
Tu reçois un corpus annoté lexicalement.
Tu produis une cartographie thématique sur 4 niveaux :

Unités (₁₂₃…)       = thèmes humains/émotionnels
                       ex. fierté, trahison, amour, deuil, solitude, désir
Dizaines (₁₀₂₀…)    = thèmes sociaux/politiques
                       ex. injustice, précarité, ascension sociale, famille
Centaines (₁₀₀₂₀₀…) = thèmes criminels/illégaux
                       ex. deal, armes, violence, drogue, menace
Milliers (₁₀₀₀…)    = thèmes introspectifs/esthétiques/méta
                       ex. identité artistique, legacy, flow, autoportrait

Pour chaque thème identifié :
- Lui assigner un code indice unique
- Le nommer avec un libellé court
- Le décrire en une phrase

Format de sortie :
## Cartographie thématique — [Artiste] / [Projet]

| Code | Libellé | Description |
|------|---------|-------------|
| ₁    | [nom]   | [description courte] |
```

Output → cartographie en mémoire pour l'étape suivante.
Lancer immédiatement l'étape 5.

---

## ÉTAPE 5 — Annotation thématique (Haiku)

Appel API Haiku :
- Entrée 1 : corpus annoté exponentiellement avec prompt caching
- Entrée 2 : cartographie thématique

System prompt :
```
Tu es un annotateur thématique spécialisé dans le rap français.
Tu reçois un corpus annoté lexicalement et une cartographie thématique.
Tu annotes le corpus vers par vers en apposant les codes indices.

Règles strictes :
- Un vers peut porter plusieurs indices si plusieurs thèmes coexistent
- Ne pas modifier le texte original ni les superscripts lexicaux existants
- Les indices thématiques s'ajoutent en gras en fin de vers : **₁₀₀**
- Les ad-libs, intros et outros sans contenu thématique net ne sont pas annotés
- Ne jamais créer de code hors cartographie fournie

Convention — trois couches distinctes :
  Lexicale        : exposant superscript  → terme¹
  Instables       : gras italique         → ***référence***
  Thématique      : gras fin de ligne     → **₁₀₀**

Exemple :
  J'ai du dry-dry¹ dans la ***Maserati***†¹ **₁₀₀₂₀**
```

Output → corpus fully annoté en mémoire pour l'étape suivante.
Lancer immédiatement l'étape 6.

---

## ÉTAPE 6 — Assemblage final (Haiku)

Appel API Haiku :
- Entrée 1 : corpus annoté exponentiellement + thématiquement
- Entrée 2 : cartographie thématique validée

System prompt :
```
Tu assembles le fichier unifié final d'un corpus rap annoté.
Tu produis un seul fichier markdown structuré ainsi :

# [ARTISTE] / [PROJET] — Corpus unifié
## CODE-BARRE : CORPUS-[NNN]-[ARTISTE]-[PROJET]

### Conventions d'annotation
[légende des trois couches : exposants / gras italique / indices]

### Légende thématique
[tableau des codes actifs avec libellés]

---
[corpus complet avec toutes les couches d'annotation]
---

===

# SYNTHÈSE — CORPUS-[NNN]-[ARTISTE]-[PROJET]

CODE-BARRE            : CORPUS-[NNN]-[ARTISTE]-[PROJET]
REGISTRE GÉNÉRAL      : [registre dominant du corpus]
LEXIQUE ARGOT VALIDÉ  :
  - terme → définition | langue d'origine
LÉGENDE THÉMATIQUE LOCALE :
  - ₁ = libellé | description courte
  [tous les codes actifs]
INDEX SÉMANTIQUE INVERSÉ :
  - libellé en clair → code indice
THÈMES DOMINANTS      : [top 3 par fréquence décroissante]
TRAITS STYLISTIQUES NOTABLES :
  [tournures récurrentes, structures syntaxiques propres à ce corpus]
COMPATIBILITÉ GÉNÉRATEUR :
  [registres avec lesquels ce corpus peut se combiner sans friction]
CONJONCTIONS POSSIBLES :
  [codes-barres des corpus antérieurs partageant des axes thématiques communs]

Le code-barre NNN s'incrémente depuis le dernier corpus traité.
Si aucun corpus précédent : NNN = 001.
```

1. Sauvegarder le fichier unifié complet
2. Extraire tout ce qui suit `===` → `synthese.md`
3. Créer le dossier `output/CORPUS-[NNN]-[ARTISTE]-[PROJET]/`
4. Déposer dans ce dossier :
   - `fichier_unifie.md`
   - `synthese.md`
5. Mettre à jour `MEMORY.md` — ajouter le corpus dans le tableau des corpus traités
6. Mettre à jour `pipeline_state.json` — toutes étapes terminées

Afficher :

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ PIPELINE TERMINÉ
Corpus : CORPUS-[NNN]-[ARTISTE]-[PROJET]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LIVRABLES DANS :
  📁 PC4/output/CORPUS-[NNN]-[ARTISTE]-[PROJET]/
     ├── fichier_unifie.md
     └── synthese.md   ← prête pour le Générateur

Lexique master : [N] termes (+[N] ajoutés ce corpus)

⚠️  ACTION REQUISE — REGISTRE TAXONOMIQUE
   → claude.ai → Projet "Registre Taxonomique"
   → Soumettre : PC4/etude/delta-[slug]-fix.md
   → Instruction :
     "Voici le registre mis à jour issu de [artiste] / [projet].
      Intègre les nouvelles catégories."

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Prochain corpus ?
→ Remplace PC4/etude/corpus_brut.md
→ Lance : pipeline start
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Commande pipeline status

Lire `pipeline_state.json` et `MEMORY.md`, afficher :

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PIPELINE STATUS
Corpus      : [artiste] / [projet]
Date début  : [date]
Modèle      : claude-haiku-4-5-20251001
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[✅/⏳/⬜] Scan delta
[✅/⏳/⬜] Désambiguïsation (H dans claude.ai)
[✅/⏳/⬜] Fusion lexique master
[✅/⏳/⬜] Annotation exponentielle
[✅/⏳/⬜] Cartographie thématique
[✅/⏳/⬜] Annotation thématique
[✅/⏳/⬜] Assemblage final
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Lexique master  : [N] termes
Corpus traités  : [N]
Prochaine action : [instruction précise]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Si `pipeline_state.json` absent :
```
⬜ Aucun pipeline en cours.
   Dépose corpus_brut.md dans etude/ et lance : pipeline start
```

---

## Commande lexique status

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LEXIQUE MASTER
Chemin          : PC4/etude/lexique_master.md
Termes validés  : [N]
Corpus sources  : [liste CORPUS-NNN]
Dernière MAJ    : [date]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Commande pipeline reset

1. Supprimer `pipeline_state.json`
2. Supprimer les fichiers temporaires dans `etude/` :
   - `delta-*.md` (tous les deltas générés)
   - `corpus_brut.md`
3. Conserver intacts :
   - `etude/lexique_master.md`
   - `output/`
   - `registre/`
   - `archives/`
4. Afficher :
```
🔄 Pipeline remis à zéro.
   lexique_master.md, output/, registre/ et archives/ conservés.
   → Dépose le nouveau corpus_brut.md dans etude/
   → Lance : pipeline start
```

---

## Ce que tu ne fais jamais

- Désambiguïser toi-même un terme — c'est le rôle de H dans claude.ai
- Passer à l'étape suivante si un fichier attendu est absent ou mal nommé
- Modifier les fichiers déposés par H
- Supprimer `lexique_master.md`, `output/`, `registre/` ou `archives/`
- Utiliser un autre modèle que `claude-haiku-4-5-20251001`
- Continuer sur un fichier mal nommé sans alerter H
