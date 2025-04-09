
#Objectif du notre partie

Notre partie du projet vise à détecter automatiquement les éléments clés d’une image de match de football (joueurs, ballon, but, etc.) grâce à YOLOv8, puis à créer un dataset enrichi contenant des informations comme la possession, la position du ballon, la densité des joueurs autour, ou encore la zone de jeu.

Ce dataset structuré a été conçu pour être exploité ensuite par d’autres membres de l’équipe, afin d’entraîner des modèles prédictifs capables d’anticiper la prochaine action de jeu (ex: passe, tir, corner, etc.).

Enfin, nous avons ajouté une partie de raisonnement probabiliste, permettant d’inférer des règles de jeu simples à partir des fréquences observées dans les données, même lorsque certaines informations sont absentes ou incertaines.

Étapes du travaille : 
1. Collecte d’images
Nous avons d’abord rassemblé une série d’images provenant de matchs de football. Ces images servent de base à l’entraînement et à la prédiction.

2. 🖍️ Annotation des images avec [Roboflow](https://roboflow.com)
Pour annoter nos images, nous avons utilisé Roboflow Annotate, une plateforme en ligne très intuitive qui permet de créer des jeux de données pour la vision par ordinateur.

Roboflow facilite l’annotation manuelle d’objets directement depuis le navigateur, en offrant une interface fluide et collaborative.
Dans notre cas, chaque image a été annotée avec soin pour identifier les éléments suivants :
	•	🎯 Ballon
	•	🧍 Joueurs (TEAM 1 / TEAM 2)
	•	🧤 Gardien
	•	⚽ Buts (goal_net)
	•	🧍‍♂️ Arbitre
	•	🚩 Corners
Roboflow propose aussi un export automatique au format YOLOv8, prêt à être utilisé pour l’entraînement de modèles
Voici un exemple issu de notre processus d’annotation :
![notation](https://github.com/user-attachments/assets/dfccb68a-5d19-4c46-841f-896978451ea4)

