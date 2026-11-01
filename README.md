# KI-GEST Android

Application mobile KI-GEST (Android). Le chargeur charge l'application web KI-GEST
dans une WebView et ajoute l'authentification par empreinte digitale et les
notifications locales.

## Telechargement (APK)

Derniere version : **2026.11.01.2** (versionCode 14)

- Installer via le QR code affiche dans KI-GEST > A propos, ou
- Telecharger directement :
  https://github.com/KI-GEST/Android/releases/latest/download/com.kigest.mobile-Signed.apk

## Nouveautes 2026.11.01.2

L'application est desormais sous licence, comme le poste web.

- **Licence demandee des l'ouverture.** Le numero de serie est remis par le revendeur
  (ki-commercial@outlook.com) et recopie sur le telephone. Il est signe pour l'identifiant de
  l'appareil : recopie sur un autre telephone, il sera refuse. Le telephone ne conserve que
  l'empreinte SHA-256 du numero, dans un fichier chiffre lie a l'appareil ; aucun secret de
  licence n'est embarque dans l'APK.
- **Activation reussie : la duree s'affiche.** « Licence activee : 365 jour(s) restant(s)
  (expire le 01/11/2027) », ou « sans limite de duree pour cet appareil » selon le numero
  remis.
- **Refus : le compteur parle.** « Licence incorrecte » avec les tentatives restantes. Trois
  numeros essayes ferment la porte 15 minutes, compte a rebours a l'ecran ; le hub tient le
  compteur, refuse meme un numero valide pendant la fermeture, et remet le compteur a trois
  des qu'une activation reussit. Une faute de forme ou un numero expire ne consomment rien.
- **Plus de demonstration de 30 jours.** Toute installation neuve de l'APK reste fermee
  jusqu'a activation.
- **Une licence activee ne se reactive pas.** Le champ de saisie disparait, et « Effacer » ne
  peut plus rouvrir la saisie d'un numero encore dans sa duree. Un numero expire, en revanche,
  se remplace.
- **Exemption = droit de session.** Superviseur et Administrateur se connectent sans licence,
  le temps de leur connexion seulement : l'exemption ne s'ecrit nulle part dans l'appareil et
  la deconnexion rend la licence exigible.

## Nouveautes 2026.10.31.3

La configuration de l'empreinte etait invisible, et un hub momentanement injoignable
laissait l'ecran blanc jusqu'a rouvrir l'application.

- **Le reglage « Connexion par empreinte » reapparait.** Le pont demandait a Android une
  feature nommee `android.hardware.biometrics`. Ce nom n'existe sur **aucun** telephone :
  ce n'est que le namespace Java. La reponse etait donc systematiquement « non », et la
  page mobile masquait sa ligne de reglage sans le moindre message. Le test porte
  desormais les noms reellement publies par la matrice de compatibilite :
  `android.hardware.fingerprint`, `android.hardware.biometrics.face`,
  `android.hardware.biometrics.iris`. Verifie sur un Galaxy A15 (Android 16) :
  `adb shell pm list features` y declare le lecteur d'empreinte, la reglage apparait
  des cette version.
- **Le reglage est dans le menu du nom.** Le toucher en haut a droite de l'ecran
  (la puce avec votre nom) ouvre la feuille ou figure « Connexion par empreinte »,
  avec son etat (Non configuree / Activee) et le bouton pour l'activer ou la couper.
- **Empreinte materielle ne veut pas dire doigt enregistre.** Nouvelle sonde
  `KxBiometric.biometricEnrolled()` : si aucune empreinte n'est enregistree dans les
  reglages Android, la ligne le dit et explique ou l'ajouter, au lieu de proposer une
  activation qui echouerait a chaque demarrage.
- **Rejeu automatique quand le hub est momentanement injoignable.** Un tunnel NetBird
  mobile endormi abandonne la premiere connexion (`ERR_CONNECTION_ABORTED`) et passe a la
  suivante : la page restait vide jusqu'a ce que l'utilisateur rouvre l'application. Trois
  relances sont desormais jouees toutes seules, a 2, 4 puis 8 secondes, avec a l'ecran
  « Hub injoignable : nouvelle tentative 1/3 dans 2 s. ». Au bout du troisieme echec, le
  message rend la main au bouton Rafraichir au lieu d'insister.

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
2026.11.01.2 s'installe **par-dessus** la precedente sans desinstallation : l'empreinte
enregistree et le mot de passe du coffre local sont conserves.

Les versions jusqu'a 2026.10.30.4 incluse etaient signees avec la cle de debug : Android
refuse une mise a jour dont la signature change (INSTALL_FAILED_UPDATE_INCOMPATIBLE). Passer
directement de 2026.10.30.4 (ou plus ancien) a la 2026.11.01.2 necessite donc une
desinstallation prealable, a reconfigurer sur le telephone.

**Mais une licence sera reclamee.** Depuis la 2026.11.01.2 il n'y a plus de demonstration de
30 jours : apres installation (ou remise a zero des donnees de l'app), l'ecran de licence
s'ouvre et demande le numero correspondant a l'identifiant affiche sur ce telephone.

## Installation

1. Autoriser l'installation d'applications "sources inconnues" si demande.
2. Ouvrir le fichier `.apk` telecharge.
3. Au premier lancement, renseigner l'adresse du hub KI-GEST (port 81 ajoute
   automatiquement s'il est oublie) puis se connecter.
