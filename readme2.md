
# 🎯 Ce que nous avons réalisé
Notre partie du projet vise à **détecter automatiquement** les éléments clés d’une image de match de football (joueurs, ballon, but, etc.) grâce à YOLOv8, puis à créer un dataset enrichi contenant des informations comme la possession, la position du ballon, la densité des joueurs autour, ou encore la zone de jeu.

Ce dataset structuré a été conçu pour être exploité ensuite par d’autres membres de l’équipe, afin d’entraîner des modèles prédictifs capables d’anticiper la prochaine action de jeu (ex: passe, tir, corner, etc.).

Enfin, nous avons ajouté une partie de raisonnement probabiliste, permettant d’inférer des règles de jeu simples à partir des fréquences observées dans les données, même lorsque certaines informations sont absentes ou incertaines.

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

### 4. 📈 Génération d’un dataset enrichi (JSON)
Après la détection, nous avons généré un fichier `.json` contenant, pour chaque image :

- Les coordonnées du ballon  
- Les joueurs autour (TEAM 1 et TEAM 2)
Voici un exemple Image prédite : 
![frame0-00-01-67_jpg rf 099c95c11d34835ed930f4e2876f5252](https://github.com/user-attachments/assets/43d24a80-836d-4967-89af-34cfacf23a88)


voici Extrait du JSON généré à partir de cette image :
![Capture d’écran, le 2025-04-09 à 15 39 25](https://github.com/user-attachments/assets/c2317782-858e-4f2c-8034-2eff98797b12)





- La zone de jeu (défense / attaque / milieu)  
- La densité de joueurs proches du ballon  
- Une action probable (`true_action`) déterminée par des règles simples  

Et surtout, nous avons ajouté plusieurs **features simulées**, permettant d'enrichir les données pour de futures prédictions :

- 📏 **Distance ballon → but** (en pixels), utile pour estimer s’il y a une menace de tir  
- 🔄 **Possession estimée**, en fonction de la proximité du ballon avec les joueurs  
- 👥 **Densité autour du ballon**, pour savoir si le joueur est pressé ou libre  
- 🧠 **Zone du terrain** (calculée à partir de la position du ballon sur l’image)  
- ⚠️ **Détection d’un corner**, même si le ballon n’est pas visible  

> Ces informations ne sont pas visibles directement dans l’image, mais elles peuvent être **calculées à partir des positions** détectées. Elles enrichissent considérablement le dataset pour l’analyse et la prédiction d’actions.
### 5. 🔍 Raisonnement probabiliste (logique floue et incertaine)

Nous avons enrichi les données extraites des images avec des informations contextuelles comme la zone de jeu, la distance du ballon au but, ou encore la densité de joueurs autour du ballon.  
À partir de là, nous avons appliqué des règles de **raisonnement probabiliste conditionnel** pour estimer l’action la plus probable selon la situation observée :

```text
📌 P(passe | TEAM 1 avec peu d’adversaires) = 0.69
📌 P(tir | distance au but < 60px)          = 0.25
📌 P(corner | ballon non détecté)           = 1.00
