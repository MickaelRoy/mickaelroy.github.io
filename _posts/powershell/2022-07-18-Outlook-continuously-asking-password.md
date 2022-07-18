---
title: "Outlook ne cesse de demander le mot de passe outlook.com/live.fr"
excerpt: "Voici un script pour mettre en correspondance votre nom de domaine avec votre adresse IP publique."
category: Hors-Series
classes: wide
tags: 
  - Outlook
  - Live
  - Mail
  - 
---

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-07-180-HS-Outlook-01.webp" alt="">
  <figcaption>Storage Spaces Direct illustration</figcaption>
</figure> 

## Outlook n’accepte pas mon mot de passe

Si Outlook n’accepte pas votre mot de passe ou plutôt, il le redemande sans cesse sans même indiquer si celui est bon ou pas, le suject tourne peut etre autour de la methode d'authentification.

A l'heure ou je vous écris chers lecteurs, Microsoft à changer sa politique d'autentification sur leurs services de messagerie. J'entends ici les compte mails ayant pour domaine, hotmail, msn, live, outlook etc...

En effet, Microsoft considerant le mot de passe comme une méthode d'autentification trop faible, nous invite à utiliser une methode dite _moderne_. Il en propose deux:
- Compte sans mot de passe
- Vérification en deux étapes

> c'est n'importe quoi... un compte sans mot de passe n'est pas plus safe qu'un compte avec un mot de passe fort.

En réalité, un compte "sans mot de passe", n'est pas sans mot de passe, ce n'est juste pas le votre.

> Adieu, _coucou123_, _amag0d_, ou encore _123456sept_...

Pour ajouter votre compte e-mail à Outlook, vous aurez donc besoin d’un _mot de passe d’application_ qui sera différent du mot de passe habituel de votre compte de courrier. CElui-ci pourra vous être donné par un _logiciel d'authentification_

## Logiciel d'autentification.

### Présentation

Un authentificateur est un moyen utilisé pour confirmer l'identité d'un utilisateur, c'est-à-dire pour effectuer une authentification numérique. Une personne s'authentifie auprès d'un système informatique ou d'une application en démontrant qu'elle possède et contrôle un authentificateur.

Pour utiliser une métode d'authentification moderne, vous devez avoir configuré au préalable un logiciel d'authentification sur votre smartphone.

Personnellement, j'utilise Microsoft Authenticator, je trouvais ca pertinent dans le mesure ou mes comptes de messageries sont tous chez microsoft, mais rien ne vous y oblige, il en éxite d'autres comme celui de Google.

### Configuration du logiciel

Non chers lecteurs, je ne vais pas vous expliquer ici comment le comfigurer. Je considère que ces logiciels sont assez intuitifs pour pouvoir les utiliser dès lors qu'ils sont installer.

[Microsoft Authenticator](https://support.microsoft.com/fr-fr/account-billing/configurer-l-application-microsoft-authenticator-comme-m%C3%A9thode-de-v%C3%A9rification-33452159-6af9-438f-8f82-63ce94cf3d29)

[Google Authenticator](https://support.google.com/accounts/answer/1066447?hl=fr&co=GENIE.Platform%3DiOS#:~:text=Configurer%20Google%20Authenticator&text=Sous%20%22Se%20connecter%20%C3%A0%20Google,%22%2C%20puis%20appuyez%20sur%20Configurer.)


## Résolution

Maintenant que vous avez le logiciel adéquat, revenons-en a la résolution de notre cas.

Le client lourd Outlook ne nous laisse plus accéder a notre messagerie Outlook.com, et c'est un comble !

Il y a une kyrielle de sources pour résoudre ce symptome.
- purger les mots de passe dans le gestionaire de mot de passe windows
- forcer les parametres s'authentification fortes outlook via la base de registre
- ...

Mais avant de vous confronter à du bidouillage qui n'a pas de sens dans la mesure où votre client de messagerie fonctionnait correctement il y a encore quelques semaines/mois, je vous invite à verifier les parametres de sécurité de votre compte Microsoft.

### Mettre à jour Office

> bah il est forcement à jour, windows le fait tout seul... non ?

Non, pour une raison que j'ignore, par defaut il se peut que votre système ne mette à jour que windows. Office ne l'est donc pas.

Ouvrez le menu Démarrer > Paramètres > Mise à jour et sécurité.

Sélectionnez Options avancées.

Sous _Choisir le mode d’installation des mises à jour_. Vous devez activer la case à cocher _Me communiquer les mises à jour d’autres produits Microsoft_.

Faites les mises-à-jours.

### Activer une métode d'authentification _moderme_

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-07-180-HS-Outlook-02.webp" alt="">
  <figcaption>Storage Spaces Direct illustration</figcaption>
</figure> 

c'est l'étape clé pour permettre à votre client lourd de se connecter

Rendez-vous sur Outlook.com et authentifiez vous.

Cliquez sur vos initiales (ou votre photo) en haut à droite, et selectionnez _Mon compte Microsoft_

Dans l'encart _Sécurité supplémentaire_ l'une des deux option doit être activé.

### Mot de passe d'application

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-07-180-HS-Outlook-03.webp" alt="">
  <figcaption>Storage Spaces Direct illustration</figcaption>
</figure> 

Cliquez sur _Créez un mot de passe d'application_

Microsoft vous redemandera de vous authentifier. Puis, il vous donnera un mot de passe à usage unique que vous pourrez utiliser dans Outlook à la place de votre mot de passe habituel.



