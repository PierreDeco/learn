---
name: visualiser
description: "Ajoute un visuel correct et minimal à une leçon — un diagramme ou une image géométrique — qui se rend en ligne dans le journal Obsidian. À utiliser quand une idée est vraiment plus claire en image : un graphe de dépendances, un système/flux, une séquence, une machine à états, un arbre, une comparaison, ou quelque chose de spatial/géométrique (géométrie de coordonnées, droite graduée, vecteurs, un tracé, un agencement physique). Sous-traite la rédaction+le rendu à un agent créateur qui vérifie l'image en la regardant, puis tu intègres le fichier renvoyé."
---

# Visualiser

Une image ne mérite sa place que si elle montre quelque chose que les mots ne peuvent pas — forme, structure, direction, relation, géométrie. Cette skill produit UNE image de ce type, garantit qu'elle est **correcte** (l'agent créateur la rend et la regarde avant de la renvoyer), et l'insère dans la leçon pour qu'elle se rende en ligne dans le fichier `.md` du journal.

Tu es le **directeur artistique**. Tu décides de l'idée exacte et la distilles à ses éléments porteurs les plus essentiels. Un **agent créateur** fait la rédaction, le rendu, la vérification visuelle et la sauvegarde, puis te renvoie un nom de fichier. Tu intègres ce nom de fichier dans ta réponse.

## Quand visualiser (et quand ne pas le faire)

Ce système d'enseignement construit un **graphe de dépendances dans la tête de l'apprenant** — axiomes à la racine, faits dérivés qui en dépendent. Une image est puissante précisément quand elle rend cette structure (ou une géométrie) visible. Utilise-en une quand :

- L'idée est une **structure ou une relation** : dépendances, un système avec des parties et des flèches, un flux/pipeline, une séquence d'échanges, une machine à états, un arbre/hiérarchie, une comparaison, un contenant (ce qui est dedans vs dehors).
- L'idée est **spatiale ou géométrique** : géométrie de coordonnées, droite graduée, vecteurs, forme d'une fonction, agencement physique.

Ne visualise PAS quand la prose ou une simple équation porte déjà l'idée. Un diagramme décoratif qui ne fait que reformuler la phrase d'à côté ajoute du bruit et un risque d'erreur. En cas de doute, abstiens-toi — un visuel manquant coûte moins cher qu'un visuel faux.

## Choisir l'agent créateur

Deux agents créateurs, définis dans `agents/` :

- **`createur-mermaid`** — visuels structurels/relationnels : graphes de dépendances, flowcharts, diagrammes de séquence/état/ER/classe, arbres, cartes mentales, frises. C'est le choix par défaut et il colle directement à la pédagogie du graphe de dépendances.
- **`createur-svg`** — visuels spatiaux/géométriques que Mermaid ne peut pas mettre en page : coordonnées exactes, figures géométriques, droites graduées, vecteurs, tracés, formes personnalisées.

Règle empirique : si c'est *des nœuds et des arêtes / des relations*, utilise `createur-mermaid`. Si c'est *des positions et des formes / de la géométrie*, utilise `createur-svg`.

## Bien briefer l'agent créateur : une idée, le moins d'éléments possible

L'échec le plus courant, c'est **l'entassement** — chaque étiquette en plus rend l'image plus difficile à lire ET plus difficile à mettre en page correctement. Avant de briefer, élague jusqu'aux éléments les plus essentiels qui portent l'idée, et pour chacun demande-toi : *« si je supprime ça, l'idée reste-t-elle claire ? »* Si oui, supprime-le.

Donne à l'agent créateur le concept ET les éléments concrets que tu veux — pas un sujet vague, et pas une longue check-list.

- MAUVAIS : « fais un diagramme sur le fonctionnement de TCP »
- BON : « graph TD : un nœud "paquet" en haut ; des flèches vers le bas vers "ordonnancement" et "retransmission en cas de perte" ; les deux flèches descendent vers "flux fiable". Pas de titre. Montre que la fiabilité est construite À PARTIR des paquets, pas à côté. »

Garde l'idée intacte mais fais confiance à l'agent créateur pour composer ; si ton brief liste plus de ~5-7 éléments, élague-le d'abord.

## Invoquer

Lance l'agent créateur avec l'outil `Agent` (`subagent_type`) :

```
Agent(subagent_type="createur-mermaid", prompt="<ton brief minimal et concret>")
```
```
Agent(subagent_type="createur-svg", prompt="<ton brief minimal et concret>")
```

L'agent créateur possède son propre déroulé (`Write`/`Edit`/`Bash` pour rédiger la source et la rendre, `Read` pour regarder le PNG) — il rédige la source, la rend en PNG, **regarde le PNG et itère jusqu'à ce que ce soit correct et propre**, publie l'image dans le dossier `viz/` du projet avec un nom de fichier unique, et renvoie :

```
RESULT:
filename: viz-<slug>-<timestamp>.png
path: <cwd>/viz/viz-<slug>-<timestamp>.png
```

S'il renvoie `RESULT: NONE`, il n'a pas pu produire une image correcte à partir du brief — simplifie, repense, ou décide que le visuel n'en vaut pas la peine. Ne rédige et ne fabrique jamais toi-même un diagramme ; la justesse dépend de la boucle rendu-puis-inspection de l'agent créateur.

## Intégrer dans la leçon

Place l'intégration directement dans ta réponse pédagogique, en utilisant le lien d'intégration wikilink d'Obsidian avec le **nom de fichier** renvoyé (pas le chemin complet) et une largeur d'affichage :

```
![[viz-<slug>-<timestamp>.png|500]]
```

C'est tout. Si une skill `/journal` est active (voir la skill `enseigner`), le contenu de ta réponse est recopié tel quel dans le fichier `.md` lié, et Obsidian résout l'intégration par nom de fichier n'importe où dans le coffre (l'agent créateur sauvegarde dans le dossier `viz` du projet, qui est dans le coffre) — donc ça se rend en ligne dans la leçon automatiquement. Une largeur `|500` est un bon défaut ; utilise plus grand pour les diagrammes denses. Introduis le visuel en une phrase, puis laisse-le porter l'idée — ne renarre pas chaque élément en prose après coup.

## Pourquoi c'est fiable

- L'agent créateur ne renvoie jamais une image qu'il n'a pas **regardée**, donc « se rend bien mais affirme quelque chose de faux » est intercepté avant d'atteindre l'apprenant.
- L'intégration en PNG garantit que **ce que l'agent créateur a vérifié est identique pixel pour pixel à ce que voit l'apprenant** — pas de dérive au re-rendu.
- Des noms de fichiers uniques gardent la résolution par nom de fichier d'Obsidian sans ambiguïté.

> Les agents créateurs rendent via des outils standards (`Bash`, `Write`, `Edit`, `Read`) — Mermaid via `@mermaid-js/mermaid-cli` (`npx`) ou un `mmdc` installé, avec Chrome/Puppeteer disponible ; SVG via `rsvg-convert`, avec repli sur ImageMagick. Assure-toi que ces outils sont installés localement (ou accessibles via `npx`) pour que le rendu fonctionne. Tu ne rends rien toi-même — tu ne fais que briefer l'agent créateur et intégrer le nom de fichier qu'il renvoie.
