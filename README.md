# learn

[![video](assets/thumbnail.png)](https://www.youtube.com/watch?v=kzcI5F4tGiU)

Mon système d'apprentissage par IA, issu de cette vidéo : [How I Use AI to Learn Things](https://www.youtube.com/watch?v=kzcI5F4tGiU).

C'est un système personnel que j'ai construit pour moi-même, partagé tel quel. Adapté pour **Claude Code** : la philosophie pédagogique encodée dans une skill, une commande, et des définitions d'agents.

> Portage de la version originale (construite pour le harnais [pi](https://github.com/earendil-works/pi)). Voir « Différences avec la version pi » plus bas.

## Contenu

- `skills/enseigner/` — la philosophie et le processus pédagogique
- `skills/visualiser/` — ajoute un diagramme correct et minimal à une leçon quand une idée est plus claire en image
- `commands/journal.md`, `commands/unjournal.md` — lient (ou délient) un fichier markdown à la session pour y recopier la leçon au fil de l'eau
- `agents/` — `chercheur`, `createur-mermaid`, `createur-svg` : les sous-agents que le système délègue

## Installation

Ce dépôt **est** un dossier `.claude`. Depuis la racine de ton projet d'apprentissage :

```bash
git clone https://github.com/amosblomqvist/learn .claude
```

Puis ouvre Claude Code dans ce dossier. (Ou copie les éléments que tu veux dans la config existante de ton projet — `skills/`, `agents/` et `commands/` de `.claude/` sont découverts automatiquement.)

## Prérequis

- [Claude Code](https://claude.com/claude-code)
- Pour la skill `visualiser` : les agents `createur-mermaid` et `createur-svg` rendent leurs images via `Bash` — installe (ou rends accessibles via `npx`) `@mermaid-js/mermaid-cli` (avec Chrome/Puppeteer) pour Mermaid, et `rsvg-convert` (ou ImageMagick en repli) pour le SVG. Sans ces outils, la skill `visualiser` échoue proprement (`RESULT: NONE`) plutôt que de produire un faux visuel.
- Les questions à l'utilisateur (préférences, quiz) utilisent l'outil natif `AskUserQuestion` de Claude Code — rien à installer.

## Notes

Tu peux utiliser le système sans les agents créateurs de visuels. La session principale fait toujours l'enseignement et peut toujours déléguer la recherche au `chercheur`. Tu perds seulement les visuels générés si les outils de rendu ne sont pas installés.

La skill `enseigner` est écrite pour un seul apprenant (moi). Édite la skill pour qu'elle corresponde à ta façon d'apprendre.

## Différences avec la version pi

La version originale ciblait le harnais **pi**, qui expose des mécanismes que Claude Code n'a pas : des extensions TypeScript avec UI de terminal custom, un tool `quiz` à notation automatique, et un hook `md-log` qui mirrore automatiquement toute la session vers un fichier markdown. Ce portage adapte chaque pièce aux primitives natives de Claude Code plutôt que de tenter une réplique à l'identique :

- **`quiz` (pi) → `AskUserQuestion` (natif) + notation par Claude.** Claude Code n'a pas d'UI de quiz notée. La skill `enseigner` utilise `AskUserQuestion` pour poser la question, ajoute manuellement une option « Je ne sais pas », puis Claude compare lui-même la réponse à la bonne réponse et donne le verdict + l'explication dans le texte qui suit.
- **`ask_user_question` (pi) → `AskUserQuestion` (natif).** Équivalent direct, rien à adapter.
- **`visual-tools` (extension pi avec outils dédiés) → `Bash`/`Write`/`Edit`/`Read` standards.** Les agents `createur-mermaid` et `createur-svg` écrivent leur source, la rendent via des CLI standards (`mmdc`, `rsvg-convert`/ImageMagick) en ligne de commande, et **regardent** le PNG résultant avec `Read` (qui affiche les images) avant de le publier — même garantie de justesse, sans extension custom.
- **`md-log` (hook automatique pi) → commande `/journal` + instructions explicites dans la skill.** Claude Code n'expose pas de hook applicatif équivalent au niveau des messages. `/journal <fichier>` demande explicitement à Claude de recopier chaque étape pédagogique dans le fichier lié, au même format de callouts Obsidian que l'original — c'est une fonctionnalité opt-in déclenchée par instruction plutôt qu'un mirroring automatique bas niveau.
- **`subagent(agent=..., task=...)` (pi) → outil `Agent` avec `subagent_type` (natif).** Les définitions d'agents (`agents/*.md`) utilisent le format de frontmatter de Claude Code (`name`, `description`, `tools`) plutôt que le format pi (`tools: ..., model: ..., thinking: ..., auto-exit: ...`).
