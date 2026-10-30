# KI-GEST Android

Application mobile KI-GEST (Android). Le chargeur charge l'application web KI-GEST
dans une WebView et ajoute l'authentification par empreinte digitale et les
notifications locales.

## Telechargement (APK)

Derniere version : **2026.10.30.5**

- Installer via le QR code affiche dans KI-GEST > A propos, ou
- Telecharger directement :
  https://github.com/KI-GEST/Android/releases/latest/download/com.kigest.mobile-Signed.apk

## Fichiers

- `version.txt` — numero de version de l'APK (1 ligne, lu par le poste pour proposer la mise a jour).
- `com.kigest.mobile-Signed.apk` — paquet installe (disponible dans la Release correspondante).

## Mise a jour depuis une version anterieure

A partir de la 2026.10.30.5, l'APK est signe avec le keystore de release KI-GEST.
Les versions precedentes (jusqu'a 2026.10.30.4 incluse) etaient signees avec la cle de
debug : Android refuse une mise a jour dont la signature change
(INSTALL_FAILED_UPDATE_INCOMPATIBLE). Il faut donc desinstaller KI-GEST une fois,
puis installer la 2026.10.30.5. Ce geste unique fait perdre l'empreinte enregistree et
le mot de passe du coffre local, a reconfigurer sur le telephone. Les montees de version
suivantes se feront sans geste preliminaire.

## Installation

1. Autoriser l'installation d'applications "sources inconnues" si demande.
2. Ouvrir le fichier `.apk` telecharge.
3. Au premier lancement, renseigner l'adresse du hub KI-GEST puis se connecter.
