# Lab Active Directory #2 — Administration et sécurité d'un environnement d'entreprise

## 📌 Présentation

Ce projet consiste à mettre en place et administrer un environnement **Active Directory** simulant le système informatique d'une petite entreprise.

L'objectif était de développer mes compétences pratiques en administration Windows, en gestion des utilisateurs et des groupes, en déploiement de stratégies de groupe, en gestion des permissions et en analyse des événements de sécurité.

Ce laboratoire a été réalisé dans un environnement virtualisé afin de reproduire des situations pouvant être rencontrées par un technicien systèmes et réseaux.

---

## 🎯 Objectifs

* Organiser un domaine Active Directory de manière structurée
* Créer et gérer des utilisateurs et des groupes
* Mettre en place des stratégies de groupe (GPO)
* Gérer les permissions NTFS
* Mettre en place des partages réseau
* Contrôler les accès aux ressources selon les services
* Mettre en place un premier niveau d'audit Windows
* Identifier des événements de sécurité dans l'Observateur d'événements
* Développer mes compétences en administration systèmes et réseaux

---

## 🖥️ Environnement technique

| Élément                | Configuration                         |
| ---------------------- | ------------------------------------- |
| Système serveur        | Windows Server 2025                   |
| Système client         | Windows 11                            |
| Virtualisation         | VirtualBox                            |
| Domaine                | `lab.local`                           |
| Contrôleur de domaine  | `SKYS1x`                              |
| Services               | Active Directory Domain Services, DNS |
| Réseau                 | Accès par pont                        |
| Gestion des stratégies | Group Policy / GPO                    |
| Partages               | SMB                                   |
| Système de fichiers    | NTFS                                  |

---

# 1. 🏢 Organisation de l'Active Directory

Afin de reproduire une organisation d'entreprise, j'ai créé une structure basée sur différents services.

### Structure

```text
lab.local
│
├── Users
│   ├── Finance
│   └── IT
│
├── Computers
│   ├── Finance
│   └── IT
│
└── Groups
```

Cette organisation permet de séparer les utilisateurs et les ordinateurs en fonction de leur service.

### Utilisateurs

Des comptes de test ont été créés pour représenter différents profils :

* Utilisateurs du service Finance
* Utilisateurs du service IT
* Compte technicien informatique
* Compte administrateur IT

Les utilisateurs sont ensuite associés à des groupes de sécurité afin de faciliter la gestion des droits.

---

# 2. 👥 Gestion des groupes

Des groupes de sécurité ont été créés afin d'attribuer les permissions aux utilisateurs de manière centralisée.

Exemples :

```text
GG_Finance
GG_IT
GG_IT_Admin
GG_Helpdesk
```

Le principe utilisé est :

```text
Utilisateur
     ↓
Groupe de sécurité
     ↓
Permission
     ↓
Ressource
```

Cette méthode permet d'éviter d'attribuer individuellement des permissions à chaque utilisateur.

---

# 3. ⚙️ Stratégies de groupe (GPO)

Des stratégies de groupe ont été mises en place afin de centraliser certaines configurations de sécurité et de gestion des postes.

Les GPO permettent à l'administrateur de contrôler les paramètres des utilisateurs et des ordinateurs appartenant au domaine.

Les configurations ont notamment été organisées en fonction des unités d'organisation (OU).

### Exemple de logique

```text
OU Finance
    ↓
GPO Finance

OU IT
    ↓
GPO IT

OU Computers
    ↓
GPO de sécurité des postes
```

Les stratégies peuvent ainsi être appliquées à un ensemble précis d'utilisateurs ou de machines.

---

# 4. 📁 Mise en place des partages réseau

J'ai créé une arborescence de dossiers permettant de simuler des ressources partagées entre différents services.

```text
C:\Shares
│
├── Finance
│
└── IT
```

Ces dossiers ont ensuite été partagés sur le réseau.

Les ressources peuvent être accessibles depuis un poste client avec un chemin UNC :

```text
\\SKYS1x\Finance
```

ou

```text
\\SKYS1x\IT
```

---

# 5. 🔐 Gestion des permissions NTFS

Des permissions NTFS ont été configurées afin de contrôler l'accès aux dossiers selon les groupes Active Directory.

### Exemple

Le groupe :

```text
GG_Finance
```

dispose de droits de modification sur :

```text
Finance
```

Le groupe :

```text
GG_IT
```

dispose de droits de modification sur :

```text
IT
```

L'objectif était de reproduire une séparation entre les ressources des différents services.

### Test réalisé

Un utilisateur Finance peut accéder à :

```text
\\SKYS1x\Finance
```

mais ne doit pas pouvoir accéder aux ressources réservées au service IT.

Inversement, un utilisateur IT peut accéder à :

```text
\\SKYS1x\IT
```

mais n'a pas automatiquement accès aux ressources Finance.

Un test d'accès refusé a également été réalisé afin de vérifier que les permissions fonctionnent correctement.

---

# 6. 🌐 Accès aux ressources depuis Windows 11

Depuis le poste Windows 11 joint au domaine, les partages ont été testés avec les comptes utilisateurs créés dans Active Directory.

Exemple :

```text
\\SKYS1x\Finance
```

Le comportement attendu était :

```text
Utilisateur Finance
       ↓
   Ressource Finance
       ↓
      ACCÈS ✅
```

et :

```text
Utilisateur Finance
       ↓
    Ressource IT
       ↓
      REFUS ❌
```

Ces tests permettent de vérifier que la gestion des permissions fonctionne correctement.

---

# 7. 🔎 Audit et Observateur d'événements

Une partie du laboratoire a été consacrée à l'observation des événements de sécurité Windows.

L'outil utilisé est :

```text
Observateur d'événements
→ Journaux Windows
→ Sécurité
```

J'ai notamment réalisé un test d'échec de connexion en utilisant volontairement un mauvais mot de passe.

L'événement observé est :

```text
Event ID : 4625
```

Il correspond à un **échec d'ouverture de session**.

L'analyse de l'événement permet notamment d'observer les informations associées à la tentative de connexion, comme le compte utilisé et l'heure de l'événement.

Cette manipulation constitue une première approche de l'analyse des journaux Windows et du troubleshooting de sécurité.

---

# 8. 🧪 Tests réalisés

Plusieurs tests ont été effectués afin de vérifier le fonctionnement de l'environnement.

### Tests Active Directory

* Création d'utilisateurs
* Création de groupes
* Organisation des utilisateurs dans les OU
* Organisation des ordinateurs dans les OU

### Tests GPO

* Application des stratégies aux OU concernées
* Vérification du comportement des postes utilisateurs

### Tests réseau

* Accès aux partages SMB
* Test d'accès aux ressources Finance
* Test d'accès aux ressources IT

### Tests de sécurité

* Tentative de connexion avec un mauvais mot de passe
* Recherche de l'Event ID 4625 dans les journaux Windows
* Vérification des informations contenues dans l'événement

---

# 9. 🛠️ Difficultés rencontrées

Ce laboratoire m'a permis de rencontrer plusieurs problèmes pratiques liés à l'administration Windows.

J'ai notamment dû vérifier :

* La bonne organisation des OU
* L'appartenance des utilisateurs aux groupes
* Les permissions NTFS
* La configuration des partages réseau
* L'utilisation correcte des chemins UNC
* Les droits d'accès aux ressources
* La présence des événements dans l'Observateur d'événements

Ces problèmes m'ont permis de développer une méthode de diagnostic basée sur la vérification progressive de la configuration plutôt que sur des modifications aléatoires.

---

# 10. 📚 Compétences développées

À travers ce laboratoire, j'ai développé des compétences dans les domaines suivants :

### Administration systèmes

* Windows Server
* Active Directory
* Gestion des utilisateurs
* Gestion des groupes
* Organisation des OU
* Gestion des ordinateurs du domaine

### Réseau

* Partages SMB
* Chemins UNC
* Accès aux ressources réseau
* Gestion des droits d'accès

### Sécurité

* Permissions NTFS
* Séparation des accès par groupe
* GPO de sécurité
* Lecture des journaux Windows
* Identification d'un échec de connexion avec l'Event ID 4625

### Dépannage

* Analyse des problèmes d'accès
* Vérification des permissions
* Vérification de la configuration Active Directory
* Analyse des événements Windows

---

# 11. 📸 Captures d'écran

Les principales étapes du laboratoire sont disponibles dans le dossier :

```text
screenshots/
```

Les captures montrent notamment :

* La structure Active Directory
* Les utilisateurs et groupes
* Les stratégies de groupe
* Les permissions NTFS
* Les partages réseau
* Les tests d'accès
* L'événement de sécurité 4625

---

# 12. 🎓 Bilan

Ce laboratoire m'a permis de mettre en pratique plusieurs notions d'administration systèmes et réseaux dans un environnement Windows Server.

J'ai notamment appris à structurer un domaine Active Directory, gérer les utilisateurs et groupes, appliquer des stratégies de groupe, contrôler l'accès aux ressources avec les permissions NTFS et analyser les journaux de sécurité Windows.

Ce projet constitue une mise en pratique de compétences que je souhaite approfondir dans le cadre d'une formation **BTS SIO SISR** et d'une future expérience en administration systèmes et réseaux.

---

## 🔧 Technologies utilisées

```text
Windows Server 2025
Windows 11
VirtualBox
Active Directory
DNS
Group Policy (GPO)
NTFS
SMB
Event Viewer
```

---

## 📌 Projet réalisé dans un environnement de laboratoire

Ce projet a été réalisé dans un environnement virtualisé personnel à des fins d'apprentissage et de développement de compétences pratiques en administration systèmes et réseaux.
