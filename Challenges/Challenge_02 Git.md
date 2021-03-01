# Enoncé du challenge Git
----

On se donne l'historique local suivant, superbement dessiné par l'extension Git Graph de VS Code.
![history](images/bad-history.png)

On souhaite modifier cet historique pour atteindre les objectifs suivants.

1. Corriger les typos du message du commit `03`. Le message doit être redressé comme suit: `03- Started Hello World Feature`

2. Permuter l'ordre des commits `02` et `03` de manière à ce que le commit _Started Hello ..._ apparaisse avant _Finished Hello..._ dans l'historique.  On doit faire ici une permutation des transactions de commit et non pas juste la permutation des messages. En effet, l'effet de l'actuelle transaction `2762d2bd` doit apparaitre avant l'effet de la transaction `87da3d43` et portera le message _02- Started Hello World Feature_. La transaction `87da3d43` portera alors le message _03- Finished Hello World Feature_.

3. Changer l'auteur du commit `05` pour qui'il soit `Me, the Challenger`. On veut cacher que `Le Pompier` était à notre secours !

4. Ecraser l'actuel commit numéro `06`. On ne veut pas divulguer cette opération critique dans l'historique par peur que certains curieux viendront y chercher des données sensibles.

5. Fusionner les 3 commits `07`, `08`, et `09` en un seul commit portant le message `Add doc`.

6. Faire en sorte que le commit `11` soit une continuité du commit `10` et qu'il en conserve le message.

## Travail demandé: 
- Recréer l'historique montré ci-haut en utilisant le script bash ci-joint.
- Travailler sur l'historique pour implémenter les 6 objectifs listés ci-haut.  Il n'est pas question de modifier le script bash, il faut travailler sur l'historique produit par ce script avec les outils git (et surtout le git rebase -i).
- Pusher votre historique modifié sur un Repo Cloud privé, auquel vous m'accordiez gentillement le droit de lecture. Y inclure un petit markdown Readme.md listant les opérations que vous avez réalisé pour atteindre chacun des objectifs.
