---
description: Lie un fichier markdown à la session pour y recopier au fil de l'eau la leçon (texte, questions, réponses) au format Obsidian.
---

Lie le fichier markdown suivant à cette session pour un journal en direct : $ARGUMENTS

Si ce chemin est vide, préviens que l'usage est `/journal <chemin-du-fichier>` et arrête-toi.

Sinon :
1. Si le fichier n'existe pas encore, crée-le (vide).
2. À partir de maintenant, et pour le reste de la session (y compris après un compactage du contexte), recopie chaque étape pédagogique dans ce fichier en respectant le format décrit dans la section « Tenir un journal de la session » de la skill `enseigner` — ajoute (n'écrase jamais) immédiatement après chaque événement : le message de l'utilisateur, ton texte pédagogique, chaque question posée (avant la réponse, sans jamais révéler la bonne réponse), puis le verdict/la réponse une fois qu'il a répondu.
3. Confirme brièvement à l'utilisateur que le journal est actif et vers quel fichier.
