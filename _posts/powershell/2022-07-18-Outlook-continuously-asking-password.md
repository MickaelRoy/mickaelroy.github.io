---
title: "Outlook ne cesse de demander le mot de passe outlook.com/live.fr"
excerpt: "Hors-Série: Continuer à utiliser Outlook pour se connecter à la messagerie Outlook.com."
category: Hors-Series
classes: wide
tags: 
  - Outlook
  - Live
  - Mail
  - Athenticator
  - Mot de passe
---

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-07-180-HS-Outlook-01.webp" alt="">
  <figcaption>Avertissement de la part de Microsoft.</figcaption>
</figure> 

## Outlook n’accepte pas mon mot de passe

Si Outlook n’accepte pas votre mot de passe ou plutôt, s'il le redemande sans cesse sans même indiquer si celui est bon ou pas, le sujet tourne peut-être autour de la méthode d'authentification.

A l'heure où je vous écris chers lecteurs, Microsoft a changé sa politique d'authentification sur leurs services de messagerie. J'entends ici les compte mails ayant pour domaine, hotmail, msn, live, outlook etc...

En effet, Microsoft considérant le mot de passe comme une méthode d'authentification trop faible, il nous invite à utiliser une méthode dite _moderne_. Il en propose deux:
- Compte sans mot de passe
- Vérification en deux étapes

> c'est n'importe quoi... un compte sans mot de passe n'est pas plus safe qu'un compte avec un mot de passe fort. Non ?

En réalité, un compte "sans mot de passe", n'est pas sans mot de passe, ce n'est juste pas le vôtre.

> Adieu, _coucou123_, _amag0d_, ou encore _123456sept_...

Pour ajouter votre compte e-mail à Outlook, vous aurez donc besoin d’un _mot de passe d’application_ qui sera différent du mot de passe habituel de votre compte de courrier. Celui-ci pourra vous être donné par un _logiciel d'authentification_.

## Logiciel d'autentification

### Présentation

Un authentificateur est un moyen utilisé pour confirmer l'identité d'un utilisateur, c'est-à-dire pour effectuer une authentification numérique. Une personne s'authentifie auprès d'un système informatique ou d'une application en démontrant qu'elle possède et contrôle un authentificateur.

Pour utiliser une méthode d'authentification moderne, vous devez avoir configuré au préalable un logiciel d'authentification sur votre smartphone par exemple.

Personnellement, j'utilise Microsoft Authenticator, je trouvais ça pertinent dans la mesure où mes comptes de messageries sont tous chez Microsoft, mais rien ne vous y oblige, il en existe d'autres comme celui de Google.

### Configuration du logiciel

Non chers lecteurs, je ne vais pas vous expliquer ici comment le configurer. Je considère que ces logiciels sont assez intuitifs pour pouvoir les utiliser dès lors qu'ils sont installés.

[Microsoft Authenticator](https://support.microsoft.com/fr-fr/account-billing/configurer-l-application-microsoft-authenticator-comme-m%C3%A9thode-de-v%C3%A9rification-33452159-6af9-438f-8f82-63ce94cf3d29)

[Google Authenticator](https://support.google.com/accounts/answer/1066447?hl=fr&co=GENIE.Platform%3DiOS#:~:text=Configurer%20Google%20Authenticator&text=Sous%20%22Se%20connecter%20%C3%A0%20Google,%22%2C%20puis%20appuyez%20sur%20Configurer.)

## Résolution

Maintenant que vous avez le logiciel adéquat, revenons-en à la résolution de notre cas.

Le client lourd Outlook ne nous laisse plus accéder à notre messagerie Outlook.com, et c'est un comble !

Il y a une kyrielle de sources pour résoudre ce symptôme.
- purger les mots de passe dans le gestionnaire de mot de passe Windows
- forcer des paramètres Outlook pour l'authentification forte via la base de registre
- ...

Mais avant de vous confronter à du bidouillage qui n'a pas de sens dans la mesure où votre client de messagerie fonctionnait correctement il y a encore quelques semaines/mois, je vous invite à vérifier les paramètres de sécurité de votre compte Microsoft.

### Mettre à jour Office

Pour que votre Outlook puisse prendre en charge ce type d'authentification, il a peut-être besoin d'être mis à jour.
C'est particulièrement vrai pour Outlook 2016 et 2019

> bah il est forcément à jour, Windows le fait tout seul... non ?

Non, pour une raison que j'ignore, par défaut il se peut que votre système ne mette à jour que Windows. Office ne l'est donc pas.

Ouvrez le menu *Démarrer* > *Paramètres* > *Mise à jour et sécurité*.

Sélectionnez *Options avancées*.

Sous _Choisir le mode d’installation des mises à jour_. Vous devez activer la case à cocher _Me communiquer les mises à jour d’autres produits Microsoft_.

Faites les mises-à-jours.

### Activer une métode d'authentification _moderne_

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-07-180-HS-Outlook-02.webp" alt="">
  <figcaption>Illustration choix de métode d'authentificationS</figcaption>
</figure> 

c'est l'étape clé pour permettre à votre client lourd de se connecter

Rendez-vous sur Outlook.com et authentifiez-vous.

Cliquez sur vos initiales (ou votre photo) en haut à droite, et sélectionnez _Mon compte Microsoft_

Dans l'encart _Sécurité supplémentaire_ l'une des deux options doit être activé.

### Mot de passe d'application

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-07-180-HS-Outlook-03.webp" alt="">
  <figcaption>Illustration créer mot de passe d'application</figcaption>
</figure> 

Cliquez sur _Créez un mot de passe d'application_

Microsoft vous redemandera de vous authentifier. Puis, il vous donnera un mot de passe à usage unique que vous pourrez utiliser dans Outlook à la place de votre mot de passe habituel.

## Conclusion

Si vous devez garder l'idée globale de ce post. Les problèmes de connexion à votre messagerie ne sont pas nécessairement liés à une mauvaise configuration de votre client de messagerie. Avant de gratter trop loin sur stack-overflow, il suffit peut-être d'utiliser les outils que l'on nous donne correctement.

Ici, il suffisait d'activer la nouvelle méthode d'authentification.


