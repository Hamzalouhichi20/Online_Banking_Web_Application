
## 👥 Ce travail a été accompli par :
### Hamza Louhichi (LOUH72370009)
### Ryan Taleb (TALR85310300)
# 🎯 Ce que nous avons réalisé
Notre partie du projet vise à **détecter automatiquement** les éléments clés d’une image de match de football (joueurs, ballon, but, etc.) grâce à YOLOv8, puis à créer un dataset enrichi contenant des informations comme la possession, la position du ballon, la densité des joueurs autour, ou encore la zone de jeu.

Ce dataset structuré a été conçu pour être exploité ensuite par d’autres membres de l’équipe, afin d’entraîner des modèles prédictifs capables d’anticiper la prochaine action de jeu (ex: passe, tir, corner, etc.).

Enfin, nous avons intégré une étape d’estimation automatique des données manquantes à l’aide d’une version simplifiée de l’algorithme EM (Expectation-Maximization), cette méthode nous a permis de compléter les coordonnées manquantes du ballon et d’ajuster dynamiquement les autres variables dérivées (possession, densité, distance au but…), assurant ainsi un dataset plus cohérent et exploitable pour les étapes d’analyse et de prédiction qui seront réalisées par les autres membres du groupe.

## 🔧 Étapes du travail
### 1. 📸 Collecte d’images
Nous avons d’abord rassemblé une série d’images provenant de matchs de football. Ces images servent de base à l’entraînement et à la prédiction.

### 2. 🖍️ Annotation des images avec [Roboflow](https://roboflow.com)
Pour annoter nos images, nous avons utilisé Roboflow, une plateforme en ligne très intuitive qui permet de créer des jeux de données pour la vision par ordinateur.

Roboflow facilite l’annotation manuelle d’objets directement depuis le navigateur, en offrant une interface fluide et collaborative.

Dans notre cas, chaque image a été annotée avec soin pour identifier les éléments suivants :
- ⚽ Ballon (Ball)  
- 🧍 Joueurs (TEAM 1 / TEAM 2)  
- 🧤 Gardien (GoalKeeper)
- 🥅 Cage de but (Goal_Net)    
- 🧍‍♂️ Arbitre(Referee)  
- 🚩 Corner

**Roboflow propose aussi un export automatique au format YOLOv8, prêt à être utilisé pour l’entraînement de modèles**

#### Voici un exemple issu de notre processus d’annotation :

![notation](https://github.com/user-attachments/assets/dfccb68a-5d19-4c46-841f-896978451ea4)

### 3. 🧠 Entraînement du modèle YOLOv8
> **YOLOv8** (You Only Look Once, version 8) est un modèle de détection d’objets **en temps réel**, développé par [Ultralytics](https://github.com/ultralytics/ultralytics).  
> Il est basé sur une architecture moderne et propose plusieurs variantes (`n`, `s`, `m`, `l`, `x`) selon le niveau de performance et de rapidité souhaité.

Nous avons utilisé le modèle **yolov8m**, que nous avons entraîné spécifiquement sur notre propre dataset de football, afin qu’il apprenne à reconnaître :

- ⚽ Ballon (Ball)  
- 🧍 Joueurs (TEAM 1 / TEAM 2)  
- 🧤 Gardien (GoalKeeper)
- 🥅 Cage de but (Goal_Net)    
- 🧍‍♂️ Arbitre(Referee)  
- 🚩 Corner

####  Avant / Après détection YOLOv8
| Avant détection                          | Après prédiction YOLOv8                      |
|------------------------------------------|----------------------------------------------|
|![frame0-00-04-00_jpg rf 833200a3db8c597daaf2254840c455cb](https://github.com/user-attachments/assets/f32b35aa-1f7a-4617-b6e4-49298ace8212) |![frame0-00-04-00_jpg rf 833200a3db8c597daaf2254840c455cb](https://github.com/user-attachments/assets/a0a5fb6b-5d2e-48e5-b0df-62f56491587b)

### 4. 📈 Génération d’un dataset (JSON)
Après la détection, nous avons généré un fichier `.json` contenant, pour chaque image :

- Les coordonnées du ballon  
- Les joueurs autour (TEAM 1 et TEAM 2)
- Position du Gardien 
- Position du cage de but
- Position du arbitre
- Position du corner
- Calcul automatique de :
	-	la distance entre le ballon et le but
	-	la densité de joueurs autour du ballon (proximité)
	-	la zone du terrain (défense / milieu / attaque)
	-	l’équipe en possession du ballon
	-	le joueur le plus proche du ballon
#### Voici un exemple Image prédite : 
![frame0-00-01-67_jpg rf 099c95c11d34835ed930f4e2876f5252](https://github.com/user-attachments/assets/43d24a80-836d-4967-89af-34cfacf23a88)


##### Voici Extrait du JSON généré à partir de cette image :
![Capture d’écran, le 2025-04-09 à 15 57 57](https://github.com/user-attachments/assets/9f3dcaaa-ed4e-4314-936e-f99185297ce0)


### 5. Estimation des données manquantes via EM (Expectation-Maximization)
Lors de l’analyse du fichier JSON généré, nous avons constaté que de nombreuses images annotées **ne contenaient pas les coordonnées du ballon**, probablement en raison de limitations de détection, d’occlusions ou d’angles cachés.  
Cette absence d’information critique a motivé l’ajout d’une étape de complétion automatique des données manquantes, essentielle pour les étapes suivantes d’analyse et de prédiction.
#### 📸 Exemple d’image où le ballon n’a **pas été détecté**

Voici une image issue de la détection YOLOv8, où tous les joueurs sont correctement annotés, **mais le ballon est manquant** :

![Design sans titre-3](https://github.com/user-attachments/assets/5cbc0c09-e7b4-4151-bb59-a98663a69409)


Pour résoudre ce problème, nous avons appliqué une **version simplifiée de l’algorithme EM (Expectation-Maximization)**, un algorithme étudié dans le cadre du cours sur le **raisonnement sous incertitude**. Il est particulièrement utile pour traiter des jeux de données partiellement observés ou incomplets.

> 🔁 **E-step (Estimation)** : Estimation des coordonnées manquantes du ballon à l’aide de la position moyenne des joueurs présents sur l’image.  
> 🔁 **M-step (Maximization)** : Mise à jour des autres attributs dérivés (`possession_team`, `closest_player_to_ball`, `density_near_ball`, etc.) à partir de la position estimée.

Cette approche nous a permis de **compléter automatiquement les données critiques absentes** et d’enrichir significativement le dataset en vue des étapes suivantes de raisonnement et de prédiction.
