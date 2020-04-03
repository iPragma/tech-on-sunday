---
title: "Configurer un nom de domaine avec Namecheap et Netlify (Méthode 1)"
date: 2020-03-31T13:39:00+01:00
description: ""
draft: false
hideToc: false
enableToc: true
enableTocContent: false
##author: Lee
tags: 
- namecheap
- netlify
-
series:
-
##slug: set-up-a-domain-name-with-namecheap-and-netlify-method-1
slug: configurer-un-nom-de-domaine-avec-namecheap-et-netlify-methode-1
---

Dans un post précédent, je montrais comment créer un site Hugo et le déployer avec Netlify. Pour avoir un site complet, il faut également connecter le site hébergé par Netlify à un nom de domaine.

Si vous achetez votre nom de domaine avec Netlify, la configuration est automatique. Si, comme moi, vous faites appel au service de Namecheap pour acheter vos noms de domaine, il existe 2 méthodes pour configurer votre nom de domaine. Vous avez le choix d'utiliser les serveurs DNS de Namecheap, ou ceux de Netlify.

Dans ce post, j'expose la 1ère méthode en utilisant les serveurs DNS de Namecheap.

## Configurer Namecheap

Dans le tableau de bord de Namecheap, cliquer sur le bouton `MANAGE` 

![alt text](./namecheap-dashboard-manage.png "namecheap dashboard manage")

Ensuite aller dans `Advanced DNS`

![alt text](./namecheap-domain-basic.png "namecheap domain basic")

Par défaut, 2 lignes sont créer pour renvoyer votre nom de domaine vers une parking page.

Dans la section `DNS TEMPLATE`, il n'existe pas de template pour Netlify, utiliser le template pour **Shopify** et valider

![alt text](./namecheap-dns-templates.png "namecheap dns templates")

Il permet de générer 2 lignes

* A Record, changer la valeur par l'IP du serveur Netlify `104.198.14.52` 
* CNAME Record pour le Host `www`, changer la valeur par l'adresse du sous-domaine Netlify de votre site

![alt text](./namecheap-dns-config.png "namecheap dns config")

Sauvegarder tous les changements en cliquant sur le bouton `SAVE ALL CHANGES`

La propagation de la configuration DNS prend un peu de temps, généralement ça se produit dans l'heure. Il est possible de vérifier l'avancée de la propagation sur un site comme [whatsmydns.net](https://www.whatsmydns.net/).

![alt text](./dns-propagation-checker.png "dns propagation checker")

## Configurer netlify

Dans la configuration du site Hugo créé, afficher les informations sur votre domaine

![alt text](./netlify-subdomain-only.png "netlify subdomain only")

Cliquer sur le bouton `Add custom domain`

![alt text](./netlify-add-custom-domain.png "netlify add custom domain")

Remplir le nom de votre domaine obtenu avec Namecheap 

![alt text](./netlify-domain-has-owner.png "netlify domain has owner")

Valider en cliquant sur `Yes, add domain`

Afficher les informations HTTPS en bas de la page de configuration du domaine 

![alt text](./netlify-verify-dns-config.png "netlify verify dns config")

Netlify peut obtenir un certificat SSL gratuitement auprès de **Let's Encrypt**. Cliquer sur `Verify DNS configuration`pour lancer la vérification de la configuration faite sur Namecheap

![alt text](./netlify-provision-certif.png "netlify provision certif")

Une fois la vérification DNS validée, rafraîchir la page pour afficher les informations sur votre nouveau certificat SSL

![alt text](./netlify-https-enabled.png "netlify https enabled")
