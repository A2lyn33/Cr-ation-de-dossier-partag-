# 📂 Créer et gérer des dossiers partagés dans Active Directory

Ce document explique pas à pas comment créer et configurer des dossiers partagés dans un environnement Active Directory, en tenant compte des bonnes pratiques en matière de permissions de partage (Share Permissions) et d’autorisations NTFS.  

---

## 🏷️ 1. Créer le dossier à partager

1. **Connectez-vous** à un serveur ou à un poste (généralement un serveur de fichiers) membre du domaine Active Directory.  
2. **Créez** un dossier ou sélectionnez un dossier existant que vous souhaitez partager.  
3. **Nommez** le dossier de manière explicite, idéalement selon votre convention de nommage (ex. : `D:\Partages\Finance`, `D:\Partages\RH`, etc.).

---

## 🔐 2. Configurer les autorisations de partage (Share Permissions)

1. **Clic droit** sur le dossier puis **Propriétés**.  
2. Allez dans l’onglet **Partage** (ou **Sharing** en anglais).  
3. Cliquez sur **Partager…** ou **Partage avancé…** (Advanced Sharing).  

   - **Via le bouton “Partager…”** :  
     - Sélectionnez les utilisateurs ou groupes de sécurité AD qui auront accès au dossier.  
     - Définissez leur niveau d’autorisation (Lecture, Modification, etc.).

   - **Via “Partage avancé…”** :  
     - Cochez la case **Partager ce dossier** (Share this folder).  
     - Dans les **Autorisations** (Permissions), définissez les droits souhaités.

**Bonnes pratiques :**  
- Attribuez souvent **Tous (Everyone) = Contrôle total** (Full Control) au niveau du partage, puis réglez les restrictions via les **autorisations NTFS** (voir section suivante).  
- Donnez un **nom de partage clair** et ajoutez un `$` si vous voulez **masquer** le partage (ex. : `Finance$`). Cela le rend invisible dans le voisinage réseau, mais il reste accessible via son chemin UNC.

---

## 🛡️ 3. Configurer les autorisations NTFS (Sécurité)

1. Dans **Propriétés** du dossier, onglet **Sécurité** (Security).  
2. Cliquez sur **Modifier** (Edit) pour gérer les **groupes** ou **utilisateurs** du domaine.  
3. Définissez les **autorisations NTFS** (Lecture, Modification, Contrôle total, etc.) pour chaque groupe ou utilisateur.  

**Bonnes pratiques :**  
- Créez des **groupes de sécurité** dédiés dans votre AD (ex. : `G_SRV_Finance_Lecture`, `G_SRV_Finance_Modification`) et attribuez-leur les droits adéquats.  
- Évitez de donner des droits directement aux utilisateurs nommés. Utilisez plutôt les groupes (principe AGDLP : Accounts -> Global Groups -> Domain Local Groups -> Permissions).  
- N’accordez que les droits **minimum nécessaires**.  

---

## ✅ 4. Tester l’accès au partage

1. Sur une **machine cliente** du domaine, ouvrez l’explorateur de fichiers.  
2. Dans la barre d’adresse, saisissez le **chemin UNC** :  
   ```text
   \\NomDuServeur\NomDuPartage
   ```
   ou  
   ```text
   \\AdresseIP\NomDuPartage
   ```
3. Vérifiez que les **droits de partage** et **NTFS** sont correctement appliqués (lecture, création, modification, suppression).

---

## 🚀 5. (Optionnel) Distribuer le partage via une GPO (Group Policy)

Pour **automatiser la création** d’un lecteur réseau sur les postes clients, utilisez une Stratégie de groupe (GPO) :

1. Ouvrez la **Gestion de stratégie de groupe** (Group Policy Management Console - GPMC).  
2. Créez ou modifiez une **GPO** liée à l’OU (Unité d’organisation) de vos utilisateurs ou ordinateurs.  
3. Dans la **Configuration Utilisateur** ou **Ordinateur**, allez dans **Préférences** > **Paramètres Windows** > **Drive Maps** (Mappages de lecteurs).  
4. Créez un **nouveau mappage** (New -> Mapped Drive) :  
   - **Location** : `\\NomDuServeur\NomDuPartage`  
   - **Drive Letter** : Lettre que vous souhaitez attribuer (ex. : `F:`).  
   - **Label as** : Donnez un nom clair (ex. : « Finance »).  
5. Fermez la console et mettez à jour les stratégies (`gpupdate /force`) ou redémarrez les machines concernées.

---

## 📝 Points clés à retenir

- **Séparer** les rôles : utilisez principalement les **autorisations NTFS** pour gérer la sécurité fine, et laissez les **autorisations de partage** en contrôle total (ou très larges) si possible.  
- **Utiliser des groupes AD** simplifie la gestion des droits : l’ajout ou la suppression d’un utilisateur dans un groupe met à jour ses autorisations, sans devoir modifier la configuration du partage.  
- **Masquer un partage** via un `$` ne le sécurise pas. Cela le rend seulement indisponible dans la liste des partages visibles.

---

<br>

> En suivant ces étapes, vous pourrez créer et gérer efficacement des dossiers partagés dans un environnement Active Directory, tout en appliquant des bonnes pratiques de sécurité et d’administration.
```
