
# Challenge 2 - Git

# Enoncé du challenge
On se donne l'historique local suivant, superbement dessiné par l'extension Git Graph de VS Code.
![history](img/bad-history.png)

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

## Solution

#### setup-repository script
````sh
#!/bin/bash

# Script : setup-repository.bas
# Author : M. Romdhani
# This scripts creates and initializes a Git Repo in a folder named repo-challenge.
# -------------------------------------------------------------------------------- 

# functions section
cleanUpFolderIfExits() {
 if [ -d "$1" ]; then
     rm -rf "$1"
     echo "Removed the existing $1..."
  fi
}
setCommitterLocally() {
  git config --local  user.name "$1"
  git config --local  user.email "$2"
}

# The Scripts Starts here

# Initializing a new repository
REPO_FOLDER="repo-challenge"
cleanUpFolderIfExits $REPO_FOLDER
echo "Setting Up the Repository in: ${DIR}..."
git init "$REPO_FOLDER"

# Go there 
cd "$REPO_FOLDER"  || exit
setCommitterLocally "Me, the Challenger" "challenger@challengeland.com"

### Making Commit 01
echo " Working on Commit 01 ..."
git commit --allow-empty -m "01- Initial commit"

### Making Commit 02
echo " Working on Commit 02 ..."
echo "Unrelated stuff!" > other.code
git add .
git commit -am "02- Finished Hello World feature"

### Making Commit 03
echo " Working on Commit 03 ..."
echo "Hello" > hello.code
git add hello.code
git commit -m "03- StArrtid Helo Volrd feature"

### Making Commit 04
echo " Working on Commit 04 ..."
echo "HelloWrld?" > hello.code
echo "Hello World!" > hello.code
git commit -am "04- Really made the thingy done"

### Making Commit 05
echo " Working on Commit 05 ..."
setCommitterLocally "LE Pompier" "super.pompier@debugland.com"
echo "HelloWrld?" > hello.code
echo "println DEBUG" > hello.code
git commit -am "05- debugging"

### Making Commit 06
echo " Working on Commit 06 ..."
setCommitterLocally "Me, the Challenger" "challenger@challengeland.com"
echo "4321pass" > private.secret
git add private.secret
git commit -m "06- important secret"

### Making Commit 07
echo " Working on Commit 07 ..."
echo "# THE Hello World program" > README.md
git add README.md
git commit -m "07- Add doc - step 1"

### Making Commit 08
echo " Working on Commit 08 ..."
echo "# THE Ultimate Hello World program" > README.md
git commit -am "08- Add doc - step 2"

### Making Commit 09
echo " Working on Commit 09 ..."
echo "" >> README.md
echo "This program does exactly what it says" >> README.md
git commit -am "09- Add doc - step 3"

### Making Commit 10
echo " Working on Commit 10 ..."
echo "should_return_true(hello.code)" > hello.test
git add hello.test
git commit -m "10- Test for feature hello world"

### Making Commit 11
echo " Working on Commit 11 ..."
echo "should_return_true(hello.code);" > hello.test
git commit -am "11- I forgot a semicolon"

# ----- End of the Script
````

#### Running the script
````sh
# grant execute permissions
/~$ chmod 751 setup-repository.bash
# launch
/~$ sudo ./setup-repository
# move to the repository
/~$ cd /repo-challenge
````

#### Display the list of commits
````sh
/repo-challenge$ git log --pretty=oneline
````
![log](img/git_log.png)

### 1. Editing commit `3` message
```sh
# use rebase to interactively edit the commit
/repo-challenge$ sudo git rebase -i master~10
```
![rebase](img/git-rebase.png)\
Change the "pick" command with "reword" in this prompt.



