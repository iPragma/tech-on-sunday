---
title: "Créer un site avec Hugo et le déployer sur Netlify"
date: 2020-01-05T10:46:06+01:00
description: ""
draft: false
hideToc: false
enableToc: true
enableTocContent: false
##author: Lee
##authorEmoji: 👺
tags: 
- hugo
- netlify
- tutoriel
image: /images/feature.png
slug: creer-un-site-avec-hugo-et-le-deployer-sur-netlify
---

**Hugo** est un formidable outil pour créer un site en quelques minutes et il existe de nombreuses solutions gratuites pour héberger le site, telles que Github, Gitlab, Netlify...

Ce tutoriel présente la solution que j'ai choisie pour mon blog, à savoir un repository **Github** dans lequel je sauvegarde le code source du site et **Netlify** pour héberger et déployer mon site **Hugo** à chaque mise à jour. 

## Prérequis

Les outils suivants sont nécessaires pour ce tutoriel :

- Git installé *-> version utilisée : 2.20.1*
- Hugo installé *-> version utilisée : 0.62.1*
- un compte Github 
- un compte Netlify (il est possible de se connecter avec son compte Github)

Si certains de ces outils sont manquants sur votre machine, une petite recherche sous Google vous permettra de trouver la procédure à suivre pour les installer.

---

## Étape 1. Créer un site avec Hugo

### Créer un projet Hugo

La commande suivante va créer un nouveau projet Hugo dans le répertoire `my-first-blog`.

    hugo new site my-first-blog
    cd my-first-blog

### Configurer un thème

Il faut d'abord télécharger un thème et l'ajouter au sous-répertoire du site `themes`. Pour ce blog, j'utilise le thème [Beautiful Hugo](https://github.com/halogenica/beautifulhugo). 

    # initialise Git
    git init

    # download theme
    git submodule add https://github.com/halogenica/beautifulhugo.git themes/beautifulhugo

Ensuite, il faut configurer le thème dans le fichier de configuration `config.toml`. Dans ce tutoriel, je vais reprendre le fichier exemple fourni avec le thème. Je ne rentre pas dans les détails, mais il faudra penser à éditer le fichier pour supprimer tout ce qui n'est pas utile.

    cp themes/beautifulhugo/exampleSite/config.toml ./config.toml

### Ajouter un post

Pour avoir un peu de contenu, il est possible de créer un post simple.

    # Add a new post
    hugo new posts/my-first-post.md

    # Change the post status to publish
    hugo undraft posts/my-first-post.md

### Tester le site en local

Enfin, le serveur inclus avec Hugo permet de visualiser le site créé.

    hugo server 

Par défaut, le nouveau site est accessible avec l'URL [http://localhost:1313](http://localhost:1313).

---

## Étape 2. Créer un repository Github

### Créer un repository Github

Dans mon compte Github, je crée un nouveau repository vide pour mon site `my-first-blog`.

![alt text](./repo-create.png "Create Repository Github")

### Ajouter tous les fichiers au repository local

    # Add the files in the local repository
    git add .

    # Commit the tracked changes
    git commit -m "First commit"

### Déclarer le repository Github et sauvegarder le projet

    # Set the new remote repository
    git remote add origin <remote repository URL>

    # Push the changes in your local repository up to the remote repository
    git push -u origin master


    echo "# with-pattaya-girl" >> README.md
    git init
    git add README.md
    git commit -m "first commit"
    git remote add origin https://github.com/iPragma/with-pattaya-girl.git
    git push -u origin master

---

## Étape 3. Déployer sur Netlify

### Configuration Netlify

Cette étape n'est pas obligatoire, mais il est parfois nécessaire de créer un fichier de configuration `netlify.toml` et de préciser la version d'Hugo pour éviter certains problèmes d'incompatibilité avec le thème choisi. 

{{< code toml >}}

[build]
publish = "public"
command = "hugo --gc --minify"

[context.production.environment]
HUGO_VERSION = "0.62.1"
HUGO_ENV = "production"
HUGO_ENABLEGITINFO = "true"

[context.split1]
command = "hugo --gc --minify --enableGitInfo"

[context.split1.environment]
HUGO_VERSION = "0.62.1"
HUGO_ENV = "production"

[context.deploy-preview]
command = "hugo --gc --minify --buildFuture -b $DEPLOY_PRIME_URL"

[context.deploy-preview.environment]
HUGO_VERSION = "0.62.1"

[context.branch-deploy]
command = "hugo --gc --minify -b $DEPLOY_PRIME_URL"

[context.branch-deploy.environment]
HUGO_VERSION = "0.62.1"

[context.next.environment]
HUGO_ENABLEGITINFO = "true"

{{< /code >}}

### Netlify Dashboard

Follow the steps on Host on Netlify to connect your Git repository to Netlify.

We’ve already configured the Hugo version for Netlify in the previous step.

If you’ve already setup a previous project with Netlify that you would like to replace, you can do that in your Netlify Dashboard. Go to your project, “Deploys” > “Continous Deployment”. You can change the Git repository, build command and publish directory.

Sélectionnez le repo de votre projet, chez moi ce sera KrustyHack/demo-nicolashug-dev et paramétrez Netlify comme ci-dessous :

<image></image>

On clique sur Deploy site et on attend. A la fin on devrait avoir une url disponible en haut de la page du projet ... Votre site est déployé !

---

**Références :**

- https://www.rockyourcode.com/move-to-hugo-with-netlify/
- https://gohugo.io/getting-started/quick-start/
- https://github.com/halogenica/beautifulhugo
- https://help.github.com/en/github/importing-your-projects-to-github/adding-an-existing-project-to-github-using-the-command-line
- https://www.freecodecamp.org/news/your-first-hugo-blog-a-practical-guide/
  