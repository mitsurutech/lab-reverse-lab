# Lab de rétro-ingénierie pour la classe : lire des programmes avec gdb, radare2, strace et ltrace

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-reverse-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-reverse-lab/actions/workflows/check.yml)

Un **lab de rétro-ingénierie** prêt à l’emploi pour apprendre à lire un programme dont on n’a pas le code source, issu de la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning), où les établissements scolaires donnent à chaque élève ses propres machines et son terminal dans le navigateur. Chaque élève reçoit une **VM** Ubuntu, gratuite à essayer, avec un compilateur, **gdb**, **radare2**, binutils, **strace** et **ltrace**, et cinq petits programmes écrits pour le lab, chacun cachant une clé. Il trouve les clés en observant les programmes, comme le ferait un défenseur ou un étudiant en compilation, et les écrit dans un fichier. Rien n’est installé sur l’ordinateur de l’élève.

Il s’agit uniquement de lire et de comprendre, sur des programmes que le lab compile lui-même. Il n’enseigne ni les logiciels malveillants, ni le contournement de la licence de logiciels réels, ni les générateurs de clés, ni la façon de cacher un programme à un débogueur, ni les exploits, ni l’examen d’un programme qui n’est pas celui du lab.

## Ce que fait l’élève

Le lab place cinq programmes dans `~/revlab` (`hello`, qui n’a pas de clé et sert à s’entraîner, puis `crack1` à `crack5`), un `answers.txt` et un `README.md` dans le dossier personnel qui explique quoi faire sans donner aucune réponse. Chaque programme affiche seulement `Access granted.` ou `Access denied.` :

1. `crack1` prend un mot de passe en ligne de commande : regarder sans l’exécuter (`file`, `strings`, `objdump`, `r2`).
2. `crack2` prend un mot de passe en ligne de commande : l’arrêter pendant son exécution dans `gdb` et regarder la mémoire.
3. `crack3` prend un nombre : lire l’arithmétique dans le désassemblage et remonter le calcul.
4. `crack4` demande un mot de passe au démarrage : observer ses appels de bibliothèque avec `ltrace`.
5. `crack5` cherche un fichier : observer ses appels système avec `strace`.
6. Écrire chaque clé dans `answers.txt`, une ligne chacune, sous la forme `crack1=la-clé`.

Derrière cela se trouve la leçon dont un défenseur a besoin : tout ce qu’un programme doit savoir pour fonctionner, un lecteur peut le trouver, donc un secret gardé dans un programme n’est pas un secret.

## Une clé différente sur chaque copie

Le dépôt est public, aucune clé n’y figure. Au premier démarrage de la VM d’un élève, `setup` crée de nouvelles clés aléatoires, les range dans un dossier que seul root peut ouvrir, construit les programmes avec elles et supprime les sources. Ces clés sont aléatoires, ce ne sont pas des valeurs plantées. Les réponses d’un élève sont comparées aux clés de sa propre copie : les réponses d’un camarade ou les fichiers du lab ne donnent rien.

## Comment il est noté

Le lab comporte une **vérification**, un script qui note une VM rendue sans que l’enseignant ait à la lire. Elle s’exécute sur une copie de la machine enregistrée, après un démarrage à neuf, et lit `answers.txt` dans le dossier personnel de l’élève. Chacune des cinq clés doit être celle créée pour cette copie de ce programme : une supposition, une ligne vide, la clé d’une autre copie ou une clé placée en face du mauvais programme ne passe pas. Elle exécute aussi chaque programme avec sa propre clé, ce qui permet de remarquer un programme modifié. La première ligne affichée dit ce qui ne va pas.

Ce qu’elle ne fait pas : elle note les réponses et non la manière de les trouver, et un élève qui utilise `sudo` sur sa propre VM pourrait lire les clés stockées. Le lab fait confiance à l’élève pour sa propre machine. C’est une vérification pour la classe, pas une preuve.

## Ce que contient le fichier

`reverse-lab.lab.yaml` est une **définition de lab VM** complète, exactement ce que la plateforme exécute :

| Clé | Ce que c’est |
| --- | --- |
| `cpus`, `memory`, `disk` | la taille de la VM de chaque élève : 1 CPU, 1024 Mo et 10 Go |
| `open` | les ports qui s’ouvrent dans le navigateur : aucun, le lab est le terminal |
| `setup` | un script exécuté une fois, en tant que root, au premier démarrage de la VM de chaque élève : il installe le compilateur et les outils depuis l’archive d’Ubuntu, crée les clés, construit les cinq programmes et écrit `answers.txt` et le README |
| `check` | le script de notation, exécuté en tant que root sur une copie du disque enregistré après un démarrage à neuf ; son code de sortie est le résultat et `0` signifie réussi |

## L’utiliser sur la plateforme

**Pour le donner comme travail à une classe :**

1. Dans votre module, ajoutez une tâche et choisissez la carte **Lab VM Ubuntu**.
2. Appuyez sur l’exemple de rétro-ingénierie, ou collez le contenu de `reverse-lab.lab.yaml`. Utiliser un exemple remplace le texte, et Annuler rétablit le vôtre.
3. Publiez et essayez d’abord votre propre copie. Chaque élève a sa propre VM, conservée d’une session à l’autre, avec ses propres clés.
4. Distribuez-le comme n’importe quelle tâche. Une fois rendu, lancez la vérification sur la ligne d’un élève dans la liste de la classe : elle démarre une copie de sa machine enregistrée, qui n’est pas sauvegardée en retour, et indique si elle a réussi et ce qu’elle a affiché.

Une leçon des bases de la plateforme se travaille dans ce lab. La [page de ce lab](https://mitsurutech.co.uk/fr/help/lab-reverse-lab) dans le manuel, son [guide des labs VM Ubuntu](https://mitsurutech.co.uk/fr/help/labs) et sa [page des exemples de labs](https://mitsurutech.co.uk/fr/help/sample-labs) disent le reste.

Pas encore de compte pour votre établissement ? [Votre première année est gratuite](https://mitsurutech.co.uk/fr/pricing) jusqu’à 50 élèves, et [l’inscription](https://mitsurutech.co.uk/fr/signup) ne demande qu’une adresse e-mail.

## Vérifier le fichier

À chaque push, `.github/workflows/check.yml` vérifie que le fichier est un YAML valide avec les clés ci-dessus et que `setup` et `check` tiennent chacun dans la limite de 16 Ko de la plateforme. Il ne démarre pas de VM : cela se fait sur les machines de la plateforme.

## Ce qui ne fonctionne pas dans un lab

La VM atteint l’archive de paquets d’Ubuntu, PyPI, npm et GitHub, et rien d’autre. Ce lab n’a besoin que de l’archive, et la copie d’un élève ne peut pas joindre Internet pour récupérer un autre programme. La préparation prend quelques minutes la première fois, le temps d’installer les outils.

## À propos

Réalisé par [Mitsuru Technologies](https://mitsurutech.co.uk/fr) pour la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning) : Python, Java, Go, Node, SQL et Linux dans le navigateur pour les écoles et les établissements, avec des labs de conteneurs, des labs VM, des applications web déployées depuis GitHub et des quiz qui se corrigent tout seuls. Essayez du code sans compte dans le [bac à sable](https://mitsurutech.co.uk/fr/playground).

D’autres labs du même éditeur : [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea), [lab-sample](https://github.com/mitsurutech/lab-sample), [lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab), [lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab), [lab-hdl-lab](https://github.com/mitsurutech/lab-hdl-lab), [lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab), [lab-tofu-lab](https://github.com/mitsurutech/lab-tofu-lab), [lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles) et [lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics).

## Licence

Cette œuvre est sous licence [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Vous pouvez la partager et l’adapter pour apprendre et enseigner, à condition de citer Mitsuru Technologies Ltd, de ne pas l’utiliser commercialement et de partager tout ce que vous en tirez sous la même licence. Le texte complet est dans le fichier `LICENSE`.

Le compilateur, gdb, radare2, binutils, strace et ltrace sont installés depuis l’archive d’Ubuntu au démarrage d’une VM ; rien n’en est distribué ici.
