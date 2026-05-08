# LAB 13 : Bypass de la Détection de Root Android avec Objection

## Cours
Sécurité des applications mobiles

---

# Objectif du LAB

Ce laboratoire permet de :

- Installer Objection
- Configurer Frida
- Bypasser la détection de root Android
- Automatiser les commandes Objection
- Tester des hooks Frida

---

# Prérequis

- Python installé
- ADB installé
- Frida installé
- Appareil Android rooté ou émulateur rooté
- USB Debugging activé

---

# Vérifications

```bash
python --version
adb version
frida --version
```

---

# Étape 1 — Installer Objection

## Installation avec pipx

```bash
pip install --user pipx
pipx ensurepath
pipx install objection
```

## Ou installation avec pip

```bash
pip install --upgrade objection
```

## Vérification

```bash
objection --version
objection --help
```

---
<img width="912" height="443" alt="Screenshot 2026-04-28 165418" src="https://github.com/user-attachments/assets/41e02594-fd89-4a27-b5d4-e715f55010ee" />

# Étape 2 — Préparer l’appareil et démarrer frida-server

## Vérifier la connexion

```bash
adb devices
```

## Push de frida-server

```bash
adb push frida-server /data/local/tmp/
```

## Permissions et démarrage

```bash
adb shell
su
chmod +x /data/local/tmp/frida-server
/data/local/tmp/frida-server &
```

## Vérification

```bash
frida-ps -U
```

---

# Étape 3 — Démarrer Objection

## Lister les packages

```bash
adb shell pm list packages
```

## Lancer Objection

```bash
objection -g com.example.app explore
```

---

# Étape 4 — Bypass Root Detection

## Désactiver la détection root

```bash
android root disable
```

Cette commande contourne :

- Vérification du binaire su
- Détection Magisk
- Vérification BusyBox
- Détection d’applications root
- Vérification des propriétés système

---

# Étape 5 — Vérifier le bypass

## Tester l’application

- Vérifier si l’application démarre
- Vérifier si les restrictions root disparaissent

## Vérifier les logs

```bash
adb logcat
```

---
<img width="1918" height="999" alt="Screenshot 2026-04-26 185110" src="https://github.com/user-attachments/assets/4f080a11-548c-4cb8-862b-3f3e555f1c2b" />

# Étape 6 — Automatiser les commandes

## Créer un fichier startup

```bash
nano startup.txt
```

## Contenu du fichier

```bash
android root disable
android sslpinning disable
```

## Lancer automatiquement

```bash
objection -g com.example.app explore --startup-command-file startup.txt
```

---

# Étape 7 — Hook natif avec Frida

## Exemple de script Frida

```bash
nano script.js
```

## Contenu

```javascript
Interceptor.attach(Module.findExportByName(null, "fopen"), {
    onEnter: function(args) {
        console.log("fopen called");
    }
});
```

## Injection du script

```bash
frida -U -f com.example.app -l script.js
```

---

# Dépannage

## Objection non trouvé

Ajouter Python Scripts au PATH.

---

## Device non détecté

```bash
adb devices
```

---

## Permission denied

```bash
chmod +x frida-server
```

---

## Versions incompatibles

```bash
frida --version
```

La version de frida-server doit être identique.

---

## Application crash

```bash
adb shell am force-stop com.example.app
```

Puis relancer Objection.

---

# Exercices

## Exercice 1

Installer Objection.

## Exercice 2

Configurer frida-server.

## Exercice 3

Bypasser la détection root.

## Exercice 4

Automatiser les injections.

## Exercice 5

Tester un hook Frida.

---

# Références

## Objection

https://github.com/sensepost/objection

## Frida

https://frida.re/docs/home/

## ADB

https://developer.android.com/tools/adb

---

# Conclusion

Dans ce laboratoire, nous avons appris à :

- Installer Objection
- Utiliser Frida
- Contourner les protections root Android
- Automatiser les commandes
- Hooker des fonctions natives

Ces techniques sont utilisées en analyse dynamique et en pentest mobile Android.
