# KI-GEST Android

Application mobile KI-GEST (Android). Le chargeur charge l'application web KI-GEST
dans une WebView et ajoute l'authentification par empreinte digitale et les
notifications locales.

## Telechargement (APK)

Derniere version : **2026.10.31.2** (versionCode 8)

- Installer via le QR code affiche dans KI-GEST > A propos, ou
- Telecharger directement :
  https://github.com/KI-GEST/Android/releases/latest/download/com.kigest.mobile-Signed.apk

## Nouveautes 2026.10.31.2

Le pont Android ne s'injectait plus depuis la 2026.10.30.5.

`Android.App.Activity` herite de `ContextWrapper` : la boucle qui remontait la chaine des
contextes pour trouver l'Activity la franchait, aboutissait a l'Application et ne
retournait donc jamais l'Activity. Les trois ponts (`KxBiometric`, `KxNotify`,
`KxUpdate`) n'etaient jamais declares a la WebView.

Consequences corrigees ici :

- **La connexion par empreinte reapparait.** `mobile-app.js` teste `window.KxBiometric` :
  pont absent, l'option s'auto-desactivait. Ce n'etait un probleme ni de capteur, ni de
  reglage KI-GEST.
- **Notifications locales et mise a jour depuis l'app** de nouveau disponibles (memes
  ponts).
- **Bandeau explicite.** Quand l'injection echoue encore, le message dit desormais POURQUOI
  (`vue WebView indisponible` ou `Activity non resolue`) au lieu d'un vague « indisponible ».
- **Recharge automatique** si le pont s'injecte apres que la page a deja ete chargee sans
  lui : le contexte JavaScript ne peut pas recevoir un pont en cours de route, il fallait
  appuyer sur Rafraichir a la main.

## Nouveautes 2026.10.31.1

Correctif du « page blanche » constate apres la saisie de l'adresse du hub.

- **Port 81 applique par defaut.** Une adresse saisie sans port (`http://100.x.x.x`)
  etait envoyee sur le port 80, ou rien n'ecoute : la WebView restait blanche sans
  message. Le port 81 du hub est desormais ajoute automatiquement. Un port écrit
  explicitement reste respecte, et `https://` garde son port 443 implicite.
- **Message d'erreur a l'ecran.** L'application affiche desormais le motif réel de
  l'echec au lieu de laisser une page vide : adresse precise, code d'erreur Android,
  description, et controle a effectuer (port 81, etat de NetBird sur le telephone).
  Une reponse du hub en erreur HTTP (4xx / 5xx) est egalement affichee.
- **Injection du pont fiabilisee.** Le pont Android (empreinte, notifications) n'etait
  tente qu'une seule fois, a un instant ou la vue native n'existait pas toujours :
  ratez cette fenetre et aucune page n'etait jamais chargee. Le pont est desormais
  reessaie jusqu'a sa reussite, avec une garde-fou qui charge la page de toute facon
  au bout de 1,2 s et l'indique a l'ecran. (Cette version rend l'echec visible ; la cause
  etait ailleurs et ne sera corrigee qu'en 2026.10.31.2.)

## Fichiers

- `version.txt` — numero de version de l'APK (1 ligne, lu par le poste pour proposer la mise a jour).
- `com.kigest.mobile-Signed.apk` — paquet installe (disponible dans la Release correspondante).

## Mise a jour depuis une version anterieure

A partir de la 2026.10.30.5, l'APK est signe avec le keystore de release KI-GEST, donc la
2026.10.31.2 s'installe **par-dessus** la precedente sans desinstallation : l'empreinte
enregistree et le mot de passe du coffre local sont conserves.

Les versions jusqu'a 2026.10.30.4 incluse etaient signees avec la cle de debug : Android
refuse une mise a jour dont la signature change (INSTALL_FAILED_UPDATE_INCOMPATIBLE).
Passer directement de 2026.10.30.4 (ou plus ancien) a la 2026.10.31.2 necessite donc une
desinstallation prealable, a reconfigurer sur le telephone.

## Installation

1. Autoriser l'installation d'applications "sources inconnues" si demande.
2. Ouvrir le fichier `.apk` telecharge.
3. Au premier lancement, renseigner l'adresse du hub KI-GEST (port 81 ajoute
   automatiquement s'il est oublie) puis se connecter.
