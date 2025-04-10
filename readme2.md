
## 👥 Ce travail a été accompli par :
### Hamza Louhichi (LOUH72370009)
### Ryan Taleb (TALR85310300)
# 🎯 Ce que nous avons réalisé
Notre partie du projet vise à **détecter automatiquement** les éléments clés d’une image de match de football (joueurs, ballon, but, etc.) grâce à YOLOv8, puis à créer un dataset enrichi contenant des informations comme la possession, la position du ballon, la densité des joueurs autour, ou encore la zone de jeu.

Ce dataset structuré a été conçu pour être exploité ensuite par d’autres membres de l’équipe, afin d’entraîner des modèles prédictifs capables d’anticiper la prochaine action de jeu (ex: passe, tir, corner, etc.).

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

