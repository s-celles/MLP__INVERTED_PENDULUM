# Projet d'un pendule inverse controlé par un modèle de Reinforcement Learning

https://github.com/user-attachments/assets/65353b49-5a06-4835-9948-fa3759f57c91

## Table des Matière

- [Introduction](#introduction)
- [Conception Mécanique et Électronique du Pendule](#conception-mécanique-et-électronique-du-pendule)

## Introduction

Ce projet se concentre sur la conception et la réalisation d'un système de contrôle pour un pendule inversé, un problème classique en robotique et en théorie du contrôle. Le défi consiste à maintenir le pendule en position verticale, un état naturellement instable, en utilisant une approche basée sur l'apprentissage par renforcement (Reinforcement Learning, RL).

L'objectif principal de ce projet est de développer une solution complète, allant de la conception mécanique et électronique du pendule, à l'entraînement d'un modèle d'IA capable de stabiliser le système, d'abord en simulation, puis sur le modèle physique réel.

Dans ce README, je vais vous guider à travers les différentes étapes de mon processus de conception du pendule, en détaillant chaque phase du projet :

1. **Conception du pendule mécanique et électronique**
2. **Détermination des caractéristiques physiques du pendule pour la simulation**
3. **Modèle de Renforcement Learning utilisé et entraînement sur la simulation**

## Conception Mécanique et Électronique du Pendule

Dans cette section, nous allons aborder la conception complète du pendule inversé. Nous commencerons par la conception mécanique. Ensuite, nous passerons à la conception électronique du pendule.

### Conception mécanique

![Shéma mécanique pendule](./Media/shema_IP.jpg)

Voici la structure mécanique du pendule. La barre rigide du pendule est fixée à un capteur angulaire qui permet une rotation libre tout en mesurant l'angle $\theta$. Ce capteur est monté sur un chariot qui peut se déplacer de manière rectiligne le long d'un axe pour maintenir la barre en équilibre. Le chariot est équipé de six roulements à billes disposés de manière à pincer un support fixe, garantissant ainsi un déplacement rectiligne sans frottement.

Pour contrôler le déplacement rectiligne, j'utilise un moteur à courant continu qui entraîne une courroie d'imprimante 3D à laquelle le chariot est attaché. Afin de mesurer la postion selon l'axe _x_ (axe de liberté du chariot), la courroie est également racordé sur un capteur angulaire qui mesure le déplacement de la courroie et en déduit la position _x_ du chariot.

Voici la liste des différents composants mécaniques externes du pendule :

| Nom                                  | Fonction                                      | Marque   | Lien utile                                                                                                                                                                                |
| ------------------------------------ | --------------------------------------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Moteur DC 12V                        | Mettre en mouvement le chariot                | ICQUANZX | [Amazon](https://www.amazon.fr/ICQUANZX-engrenages-r%C3%A9duction-%C3%A9lectrique-Diam%C3%A8tre/dp/B0824V7YGT)                                                                            |
| Encoder (400p & 1000p)               | Mesurer la position _x_ et l'angle $\theta$   | OMRON    | [Datasheet](https://assets.omron.eu/downloads/latest/datasheet/en/q085_e6b2-c_incremental_rotary_encoder_40_mm_datasheet_en.pdf)                                                          |
| Rouelement à bille (625-ZZ & 635-ZZ) | Assurer le déplacement du chariot             | ROLAKIN  | [625-ZZ](https://www.123roulement.com/roulement-palier/roulement-bille/simple-rangee/625-zz) [635-ZZ](https://www.123roulement.com/roulement-palier/roulement-bille/simple-rangee/635-zz) |
| Courroie imprimante 3D               | Transmettre le mouvement du moteur au chariot | TUZUK    | [Amazon](https://www.amazon.fr/gp/product/B08SWTSG2K)                                                                                                                                     |

Ensuite, pour fabriquer les différentes pièces de mon pendule, j'ai modélisé l'ensemble du système à l'aide du logiciel **Fusion 360**. J'ai ensuite exporté les différents plans de coupe de ma modélisation et j'ai découpé mes pièces sur une plaque de plexiglas, à l'aide d'une découpeuse laser. Les pièces ont ensuite été assemblées avec des vis et d'écrous, facilitant ainsi le remplacement de composant.

J'ai opté pour un design adapté à la découpe laser plutôt que de l'impression 3D en raison de :

- La précision des découpes,
- La rapidité de fabrication,
- Le prix de la matière première.

Malheureusement, certaines pièces plus complexes ont dû être imprimées en 3D en raison de leur besoin de symétrie circulaire (l'empout entre la courroie et le moteur) ou nécessitant une épaisseur supérieur à 3mm (les pieds du support).

Je vous laisse voir le [dossier Conception](/Conception) pour avoir accès aux fichiers _Fusion 360_.

### Conception éléctronique

Pour contrôler le moteur du pendule inverse et collecter les données en temps réel, nous utilisons un intermédiaire, une carte Arduino. Celle-ci capte les informations des capteurs et les transmet à un ordinateur via USB. Ensuite, l'Arduino reçoit les commandes de contrôle générées par le modèle et les applique pour stabiliser le pendule. Dans cette partie, nous allons étudier le shéma électrique permettant la récupéretion des données par les capteurs et le contrôle du moteur.

![Shéma électrique pendule](./Media/electric_shema.jpg)

#### Capteurs

J'ai utilisé des capteurs de rotation incrémental pour mesurer avec précision l'angle et la position du pendule. Le fonctionnement de ces capteurs est détaillé dans cette [vidéo youtube](https://www.youtube.com/watch?v=zzHcsJDV3_o). Les codeurs incrémentaux optiques sont avantageux grâce à leur coût relativement bas tout en offrant une grande précision. Cependant, ces capteurs fournissent des mesures d'angles relatifs plutôt que des mesures absolus. Ainsi, il est nécessaire de calibrer nos capteurs à chaque démarrage de l'Arduino afin d'établir une position référentiel.
