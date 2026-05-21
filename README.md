# LAB 13 : Bypass de la Détection de Root Android avec Objection
 
**Cours : Sécurité des applications mobiles**  
**Application cible : Uncrackable1**  
**Outils utilisés : Frida, Frida-server, Objection, ADB, Android Emulator**

---

## 1. Objectif du laboratoire

L’objectif de ce laboratoire est de comprendre comment une application Android peut détecter qu’un appareil est rooté, puis d’utiliser les outils Frida et Objection dans un environnement de test contrôlé afin d’analyser et contourner cette détection.

L’application utilisée dans ce lab est **Uncrackable1**, une application de test issue du contexte OWASP MSTG. Elle affiche un message d’erreur lorsqu’elle détecte un environnement rooté.

---

## 2. Prérequis

Avant de commencer le laboratoire, il faut disposer des éléments suivants :

- Python installé sur Windows
- ADB installé et accessible depuis le terminal
- Frida installé côté PC
- Frida-server compatible avec l’architecture de l’émulateur Android
- Objection installé
- Un émulateur Android rooté
- L’application Android Uncrackable1 installée

---

## 3. Vérification de Frida et installation de pipx

La première étape consiste à vérifier que Frida est bien installé sur la machine Windows.

Commande utilisée :

    frida --version

Résultat obtenu :

    17.9.1

Ensuite, nous installons ou vérifions l’installation de pipx avec la commande suivante :

    pip install --user pipx

Puis nous vérifions que les chemins nécessaires sont bien ajoutés dans la variable PATH :

    pipx ensurepath

Le terminal indique que les dossiers nécessaires sont déjà présents dans le PATH, ce qui confirme que l’environnement Python est correctement configuré.

### Capture d’écran


<img width="1971" height="798" alt="image" src="https://github.com/user-attachments/assets/9bf2aac3-b9b2-447d-bb3b-8701b85e8bc9" />


---

## 4. Vérification de l’installation d’Objection

Après la configuration de Frida et pipx, nous vérifions que l’outil Objection est bien installé.

Commande utilisée :

    objection --help

Cette commande affiche l’aide complète d’Objection avec les différentes options disponibles, comme :

- la connexion via USB ou réseau
- le choix du host
- le choix du port
- le choix du package Android cible
- le mode debug
- le lancement ou l’attachement à une application

L’affichage de cette aide confirme que l’outil Objection est bien installé et prêt à être utilisé.

### Capture d’écran

<img width="1031" height="490" alt="image" src="https://github.com/user-attachments/assets/39bb9a21-a813-4247-97bd-5a0cc35198f6" />


---

## 5. Préparation de l’émulateur Android et lancement de frida-server

Dans cette étape, nous préparons l’émulateur Android pour exécuter Frida-server.

Nous vérifions d’abord l’architecture CPU de l’appareil Android avec la commande :

    adb shell getprop ro.product.cpu.abi

Résultat obtenu :

    x86_64

Cela signifie que nous devons utiliser une version de frida-server compatible avec l’architecture x86_64.

Ensuite, nous activons le mode root avec ADB :

    adb root

Résultat obtenu :

    adbd is already running as root

Puis nous envoyons le fichier frida-server vers l’émulateur Android :

    adb push frida-server /data/local/tmp/

Ensuite, nous donnons les droits d’exécution au fichier :

    adb shell chmod 755 /data/local/tmp/frida-server

Enfin, nous lançons frida-server sur l’appareil Android :

    adb shell "/data/local/tmp/frida-server -l 0.0.0.0"

Cette étape permet à Frida et Objection de communiquer avec l’application Android pendant son exécution.

### Capture d’écran

<img width="853" height="163" alt="image" src="https://github.com/user-attachments/assets/00b36985-2a4a-4ce2-aa1e-61902d596782" />


---

## 6. Détection du root par l’application

Après le lancement de l’application Uncrackable1 sur l’émulateur Android, l’application détecte que l’appareil est rooté.

Elle affiche alors le message suivant :

    Root detected!
    This is unacceptable. The app is now going to exit.

Ce message montre que l’application contient un mécanisme de protection empêchant son exécution sur un appareil rooté.

Cette étape permet de confirmer que la détection root fonctionne correctement avant le test de contournement.

### Capture d’écran

<img width="1824" height="862" alt="image" src="https://github.com/user-attachments/assets/1fefad22-ae85-49d1-a9a2-652299bb2783" />


---

## 7. Lancement d’Objection sur l’application cible

Après avoir démarré frida-server, nous lançons Objection sur le package de l’application cible.

Commande utilisée :

    objection -n owasp.mstg.uncrackable1 -s -p start

Objection se connecte à l’application Android et démarre une session interactive.

Une fois dans la console Objection, nous pouvons exécuter la commande suivante :

    android root disable

Cette commande permet de désactiver certains contrôles de détection root dans le cadre de ce laboratoire.

Ensuite, nous pouvons rechercher les classes contenant le mot root :

    android hooking search classes root

Puis rechercher les méthodes contenant isRoot :

    android hooking search methods isRoot

Ces commandes permettent d’analyser dynamiquement l’application afin d’identifier les fonctions liées à la détection root.

### Capture d’écran

<img width="897" height="1754" alt="image" src="https://github.com/user-attachments/assets/a70480b1-4966-4fd2-bc5f-1d6f3985ca28" />


---

## 8. Validation sur l’émulateur Android

Après l’utilisation d’Objection, nous retournons vers l’émulateur Android afin de vérifier l’état de l’application.

L’application Uncrackable1 est ouverte et affiche le champ :

    Enter the Secret String

ainsi que le bouton :

    VERIFY

Cette étape permet de valider que l’application peut être relancée et testée après les manipulations réalisées avec Frida et Objection.

### Capture d’écran

<img width="1140" height="1380" alt="image" src="https://github.com/user-attachments/assets/ee07ab3f-e8f7-4ce2-aef8-0f0855f98c83" />


---

## 9. Résumé des commandes utilisées

Voici les principales commandes utilisées dans ce laboratoire :

    frida --version

    pip install --user pipx

    pipx ensurepath

    objection --help

    adb shell getprop ro.product.cpu.abi

    adb root

    adb push frida-server /data/local/tmp/

    adb shell chmod 755 /data/local/tmp/frida-server

    adb shell "/data/local/tmp/frida-server -l 0.0.0.0"

    objection -n owasp.mstg.uncrackable1 -s -p start

    android root disable

    android hooking search classes root

    android hooking search methods isRoot

---

## 11. Conclusion

Ce laboratoire a permis de comprendre le fonctionnement général d’une détection root dans une application Android.

Nous avons d’abord préparé l’environnement de travail avec Python, pipx, Frida et Objection. Ensuite, nous avons configuré l’émulateur Android, lancé frida-server, observé le message de détection root dans l’application Uncrackable1, puis utilisé Objection pour analyser dynamiquement l’application.

Les étapes principales réalisées sont :

- vérification de Frida
- installation et vérification de pipx
- vérification d’Objection
- préparation de l’émulateur Android
- lancement de frida-server
- observation de la détection root
- lancement d’Objection
- analyse des classes et méthodes liées au root
- validation finale sur l’émulateur

Ce travail est réalisé uniquement dans un cadre pédagogique et dans un environnement de laboratoire contrôlé.

---


LAB 13 : Bypass de la Détection de Root Android avec Objection  
Cours : Sécurité des applications mobiles
