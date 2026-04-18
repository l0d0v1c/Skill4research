# Guide d'utilisation du skill CLAUDE.md

**Auteur :** Luc Emile Brunet (l0d0v1c) et pseudoLuc
**Version :** 1.1
**Licence :** MIT

---

## 1. Qu'est-ce que ce skill ?

`CLAUDE.md` est un fichier de directives qui transforme Claude Code d'un assistant de programmation en un **partenaire de recherche structuré**. Il applique les principes de la méthodologie RDI (Recherche Développement Innovation) de Luc Emile Brunet, notamment :

- La **production d'alternatives** avec estimation d'effort avant toute action.
- La **traçabilité cumulative** des observations, anomalies et leçons apprises.
- La **vérification systématique** des références bibliographiques.
- Les **revues de situation** déclenchées par des signaux de blocage.

Ces principes sont détaillés dans :

> BRUNET, L. E. *Créativité humaine et augmentée au service de l'innovation.* vol. AG5210V2, Editions Techniques de l'ingénieur, 2021.
>
> BRUNET, L. E. *Recherche développement innovation (RDI) en entreprise.* Techniques de l'ingénieur, base documentaire TIB564DUO, 2016.

Le codage n'est qu'un cas particulier de production structurée de langage. Ce skill traite toute investigation — revue de littérature, conception expérimentale, analyse de données, rédaction scientifique — avec la même rigueur.

---

## 2. Installation

### Dans Claude Code (CLI, desktop, web, IDE)

Copier le fichier `CLAUDE.md` à la racine de votre projet :

```bash
cp CLAUDE.md /chemin/vers/votre/projet/
```

Claude Code détecte automatiquement le fichier et applique les directives dès le début de la session.

### En tant que prompt (sans Claude Code)

Si vous utilisez Claude via l'API ou l'interface conversationnelle sans Claude Code, copiez le contenu intégral de `CLAUDE.md` dans le **system prompt** ou au début de votre conversation. L'effet est le même : Claude adopte le comportement décrit.

---

## 3. Méthodologie

### 3.1. Démarrage de session

À chaque nouvelle session, Claude suit un protocole strict :

1. Lire `spec.md` — la source de vérité du projet.
2. Lire `session_log.md` — pour reprendre le contexte de la session précédente.
3. Lire `findings.md`, `surprises.md`, `rex.md` si pertinent.
4. Lire `requirements.md` avant toute installation.
5. Énoncer en deux lignes l'état compris du projet avant d'agir.

Ce protocole garantit la **continuité entre sessions** : rien n'est perdu, rien n'est deviné.

### 3.2. Clarifications et alternatives

Avant de produire du code ou du texte, Claude :

- Pose des **questions de clarification une par une** (jamais en lot).
- Propose **2 à 4 alternatives** pour toute tâche non triviale.

Chaque alternative est annotée d'un **score d'effort** :

| Score | Signification |
|---|---|
| **1** | Quelques minutes, modification locale |
| **2** | Une à deux heures, implémentation simple |
| **3** | Une journée, intégration de plusieurs composants |
| **4** | Plusieurs jours, conception ou refactoring partiel |
| **5** | Une semaine ou plus, travail structurel |

Format attendu :

```markdown
**Option 1 — Nom court**
Description en deux à quatre lignes. Ce qui est fait, ce qui est obtenu.
Compromis : ce qu'on gagne, ce qu'on perd.
Effort : **2** (quelques heures)
```

### 3.3. Revue de situation

Claude déclenche une **revue de situation** quand :

- Trois itérations sans progrès mesurable.
- La même erreur récurrente deux fois sans résolution.
- Divergence entre l'état du projet et `spec.md`.
- Sentiment que le cadrage initial ne tient plus.

La revue suit un format précis : problème en une phrase, hypothèses actives, contraintes, 2 à 3 chemins de reprise.

### 3.4. Reproductibilité

- Seeds fixées dans tout code stochastique.
- Versions exactes dans `requirements.md` (`==1.2.3`, jamais `>=1.2`).
- Commandes d'installation explicites, pas juste des noms de paquets.

---

## 4. Les fichiers canoniques

Le skill organise la connaissance du projet en sept fichiers à la racine. Chaque fichier a une fonction unique, sans chevauchement. Les ajouts sont **cumulatifs et datés** — on ne remplace jamais, on étend.

### 4.1. `spec.md` — Spécification du projet

La source de vérité. Tout le reste se réfère à ce fichier. Claude le lit en premier à chaque session.

### 4.2. `session_log.md` — Journal de session

Entrée légère à chaque fin de session : date, travail réalisé, état courant, tâches en attente, point de reprise.

```markdown
## 2026-04-18 — Session 7

**État au début de la session :** pipeline d'entraînement fonctionnel,
corpus nettoyé, première baseline en place (accuracy 0.71).

**Fait :**
- Ajout du module d'évaluation sur le jeu de test.
- Benchmark comparatif contre baseline HuggingFace.

**En attente :**
- Vérifier si le biais de classe observé tient sur un second corpus.

**Prochaine session :** reprendre par l'analyse du biais de classe.
```

### 4.3. `findings.md` — Observations structurelles

Observations qui alimentent la spécification. Pas les anomalies ponctuelles.

```markdown
## 2026-04-18 — Les embeddings se stabilisent après 3 epochs
Sur 5 exécutions de test (seeds variées), la loss de validation
plafonne à partir de l'epoch 3. Les epochs 4-10 n'apportent rien.
**Implication :** le budget d'entraînement peut être divisé par 3.
```

### 4.4. `surprises.md` — Découvertes inattendues

Anomalies, comportements imprévus. Pas nécessairement structurel.

```markdown
## 2026-04-18 — Le tokenizer BPE coupe les URLs à "https"
Le tokenizer traite `https://` comme un token unique suivi d'un
fragment. Conséquence : les URLs d'entrée perdent leur structure.
Non bloquant pour l'instant.
```

### 4.5. `rex.md` — Retours d'expérience

Échecs rencontrés et comment ils ont été résolus.

```markdown
## 2026-04-18 — Crash mémoire sur batch > 32
**Problème :** OOM sur GPU 24 Go avec batch_size=64.
**Cause :** accumulation de gradient mal configurée, buffer non libéré.
**Résolution :** `torch.cuda.empty_cache()` après chaque accumulation
+ batch_size effectif=32 avec accumulation=2.
**Temps de résolution :** 3h.
```

### 4.6. `biblio.md` — Références bibliographiques

En format BibTeX. Chaque référence est vérifiée (voir section 5).

### 4.7. `requirements.md` — Dépendances

Bibliothèques, logiciels, modèles et outils nécessaires avec versions exactes et commandes d'installation.

---

## 5. Gestion bibliographique

### 5.1. Vérification des références

Le skill impose une vérification systématique de chaque référence :

1. **DOI** : vérifié via l'API Crossref (`https://api.crossref.org/works/{doi}`).
2. **ISBN** : vérifié via WorldCat ou équivalent.
3. **URL** : page récupérée pour confirmer son existence.

Si la vérification échoue, la référence est marquée `% UNVERIFIED` et signalée à l'utilisateur. **Aucune référence n'est inventée.**

### 5.2. Format BibTeX dans `biblio.md`

```bibtex
@article{vaswani2017attention,
  author  = {Vaswani, Ashish and Shazeer, Noam and Parmar, Niki and others},
  title   = {Attention is all you need},
  journal = {Advances in Neural Information Processing Systems},
  year    = {2017},
  volume  = {30},
  doi     = {10.48550/arXiv.1706.03762},
  url     = {https://arxiv.org/abs/1706.03762}
}
% Verified: Crossref OK (2026-04-18), URL fetched OK.
```

### 5.3. Importer `biblio.md` dans Zotero

Le fichier `biblio.md` contient du BibTeX valide. Pour l'importer dans Zotero :

**Méthode 1 — Import direct**

1. Extraire les entrées BibTeX de `biblio.md` dans un fichier `.bib` :
   ```bash
   grep -A 20 '^@' biblio.md | grep -v '^%' > biblio.bib
   ```
   Ou plus simplement, copier les blocs `@article{...}`, `@book{...}`, etc. dans un fichier `biblio.bib`.

2. Dans Zotero : **Fichier > Importer...** > sélectionner `biblio.bib` > choisir la collection cible > **Suivant**.

3. Zotero crée une entrée pour chaque référence avec tous les champs renseignés (auteurs, titre, DOI, ISBN, URL).

**Méthode 2 — Better BibTeX (recommandé)**

1. Installer le plugin [Better BibTeX for Zotero](https://retorque.re/zotero-better-bibtex/).
2. **Fichier > Importer...** > sélectionner `biblio.bib`.
3. Better BibTeX gère les clés de citation, les caractères spéciaux et les champs non standard de manière plus robuste.
4. Option avancée : configurer une **synchronisation automatique** pour que Zotero surveille le fichier `.bib` et importe les nouvelles entrées.

**Méthode 3 — Via DOI dans Zotero**

Puisque le skill vérifie les DOI, vous pouvez aussi :

1. Dans Zotero : cliquer sur l'icône **Ajouter par identifiant** (baguette magique) dans la barre d'outils.
2. Coller le DOI (ex. `10.48550/arXiv.1706.03762`).
3. Zotero récupère automatiquement les métadonnées complètes depuis Crossref.

Cette méthode est la plus fiable car Zotero utilise directement les métadonnées de l'éditeur.

**Astuce : script d'extraction**

Pour automatiser l'extraction des DOI depuis `biblio.md` :

```bash
grep -oP 'doi\s*=\s*\{([^}]+)\}' biblio.md | sed 's/doi\s*=\s*{\(.*\)}/\1/'
```

---

## 6. Workflow type d'une investigation

Voici un déroulement type d'utilisation du skill pour une investigation scientifique :

### Étape 1 — Initialisation du projet

```
mkdir mon-investigation
cp CLAUDE.md mon-investigation/
cd mon-investigation
claude   # ou ouvrir Claude Code dans ce répertoire
```

Claude vous demande de créer `spec.md` — le point de départ de toute investigation.

### Étape 2 — Rédaction de la spécification

Décrivez votre question de recherche, le périmètre, les contraintes. Claude vous aide à la structurer via des questions de clarification posées une par une.

### Étape 3 — Exploration et alternatives

Pour chaque tâche (revue de littérature, choix de méthode, analyse), Claude propose 2 à 4 alternatives avec score d'effort. Vous choisissez, il exécute.

### Étape 4 — Accumulation des connaissances

Au fil des sessions :
- Les observations structurelles vont dans `findings.md`.
- Les anomalies dans `surprises.md`.
- Les échecs et résolutions dans `rex.md`.
- Les références vérifiées dans `biblio.md`.
- Le journal de session dans `session_log.md`.

### Étape 5 — Revues de situation

Si le travail stagne, Claude déclenche une revue structurée : problème, hypothèses, contraintes, chemins de reprise.

### Étape 6 — Synthèse

En fin de projet, les fichiers canoniques constituent une **base de connaissance complète et traçable** de l'investigation.

---

## 7. Utilisation sans Claude Code (en prompt)

Si vous n'avez pas accès à Claude Code, le contenu de `CLAUDE.md` fonctionne comme **system prompt**. Collez-le au début de votre conversation avec Claude (API, interface web, ou tout autre client).

Limitations par rapport à Claude Code :

| Fonctionnalité | Claude Code | Prompt seul |
|---|---|---|
| Lecture automatique des fichiers canoniques | Oui | Non — vous devez coller le contenu |
| Création et mise à jour des fichiers | Oui | Non — vous devez copier les sorties manuellement |
| Délégation aux sous-agents | Oui | Non |
| Vérification DOI/ISBN via web | Oui | Selon le client utilisé |
| Continuité entre sessions | Automatique via `session_log.md` | Manuelle — coller le log en début de session |

En mode prompt, le skill reste utile pour la **discipline méthodologique** : alternatives avec effort, clarifications avant action, format des observations.

---

## 8. Bonnes pratiques

1. **Toujours commencer par `spec.md`.** Sans spécification, le travail dérive.
2. **Ne pas sauter les questions de clarification.** Elles évitent les itérations inutiles.
3. **Faire confiance au score d'effort.** Un effort 4 sous-estimé en effort 2 produit de la dette technique.
4. **Alimenter `surprises.md` même pour les petites anomalies.** Ce qui semble anodin aujourd'hui peut être structurel demain.
5. **Exporter régulièrement `biblio.md` vers Zotero.** Le BibTeX est un format d'échange, pas de stockage long terme.
6. **Relire `session_log.md` en début de session.** Claude le fait automatiquement ; vous devriez aussi.

---

## 9. Références

```bibtex
@article{brunet2021creativite,
  author    = {Brunet, Luc Emile},
  title     = {Créativité humaine et augmentée au service de l'innovation},
  journal   = {Techniques de l'ingénieur},
  year      = {2021},
  volume    = {AG5210V2},
  publisher = {Editions Techniques de l'ingénieur}
}

@article{brunet2016rdi,
  author    = {Brunet, Luc Emile},
  title     = {Recherche développement innovation (RDI) en entreprise},
  journal   = {Techniques de l'ingénieur},
  year      = {2016},
  note      = {Base documentaire TIB564DUO, Management et ingénierie
               de l'innovation}
}
```
