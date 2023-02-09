# workshop-raycasting

## Installation des outils

### Installer p5.js

Pour installer p5.js, il faut télécharger le fichier zip sur le site officiel : https://p5js.org/download/

Puis cliquer sur le lien **p5.js complete** pour télécharger le fichier zip.

Ensuite, il faut décompresser le fichier zip dans le dossier de votre choix.

Nous allons ensuite ouvrir ce répertoire avec vscode.

---

### Installer Live Server

Pour installer Live Server, il faut afficher l'installeur de package vscode en appuyant sur **Ctrl+Shift+X**.

Ensuite, il faut taper **Live Server** dans la barre de recherche.

Enfin, il faut cliquer sur le bouton **Install**.

Ce plugin va permettre de lancer un serveur web local pour pouvoir tester notre code en temps réel.

---

## Les différentes étapes

### Initialisation de l'écran

Nous allons commencer par éditer le fichier **sketch.js** afin de créer une scène avec un fond noir.

```js
// La fonction setup est appelée une seule fois au démarrage du programme
function setup() {
	// Création du canvas de la taille de la fenêtre
	createCanvas(windowWidth, windowHeight)
	// initialisation de l'écran
	initialize()
}

// La fonction draw est appelée à chaque frame
function draw() {
	// Affichage du fond noir
	background(0)
}

// La fonction windowResized est appelée à chaque fois que la taille de la fenêtre change
function windowResized() {
	// On redimensionne le canvas
	resizeCanvas(windowWidth, windowHeight)
	// On réinitialise l'écran
	initialize()
}

// La fonction initialize est appelée à chaque fois que la taille de la fenêtre change
function initialize() {
	// On initialise l'écran
	screen.w = width
	screen.h = height
}

// L'objet screen contient les dimensions de l'écran
const screen = {
	w: 0,
	h: 0,
}
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/bGYdeLG)

---

### Dessiner les contours de la scène

Pour dessiner les contours de la scène, nous allons :

- Ajouter une variable walls qui contiendra la liste des murs sachant qu'un mur est un segment. Un segment est une liste de deux points créés avec la fonction **createVector()**;

```js
const walls = []
```

- Modifier la fonction **initialize()** pour ajouter les murs autour de la scène ainsi qu'un mur vertical au milieu de la scène;

```js
walls.length = 0
walls.push([createVector(0 * screen.w, 0 * screen.h), createVector(1 * screen.w, 0 * screen.h)])
walls.push([createVector(1 * screen.w, 0 * screen.h), createVector(1 * screen.w, 1 * screen.h)])
walls.push([createVector(1 * screen.w, 1 * screen.h), createVector(0 * screen.w, 1 * screen.h)])
walls.push([createVector(0 * screen.w, 1 * screen.h), createVector(0 * screen.w, 0 * screen.h)])
// TODO: ajouter un mur vertical au milieu de la scène
```

- Modifier la fonction **draw()** pour dessiner les murs à l'aide de la fonction **drawSegment()**;

```js
stroke(255)
strokeWeight(5)
// TODO: dessiner les murs
```

- Créer la fonction **drawSegment()** qui prend en paramètre un segment et qui dessine ce segment.

```js
function drawSegment([p0, p1]) {
	// TODO: dessiner le segment
}
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/NWwqrJx)

---

### Dessiner le player

Nous allons représenter le player par un cercle ainsi que sa direction par une ligne en procédant comme suit :

- Ajouter une variable player qui contiendra la position du player ainsi que sa direction;

```js
const player = {
	pos: null,
	ang: null,
}
```

- Modifier la fonction **initialize()** pour initialiser le player;

```js
player.pos = createVector(0.25 * screen.w, 0.25 * screen.h)
player.ang = 0
```

- Créer la fonction **getRay()** qui prend en paramètre un angle à ajouter à la direction du player et et une distance et qui retourne un rayon. Un rayon est un segment dont le point de départ est la position du player et le point d'arrivée est calculé à partir de l'angle et de la distance.

```js
const getRay = (ang, dist) => {
	// TODO: retourner le rayon
}
```

- Modifier la fonction **draw()** pour dessiner le player ainsi que la direction du player;

```js
stroke(255)
strokeWeight(10)
// TODO: dessiner le player

const ray = getRay(0, 100)
strokeWeight(1)
// TODO: dessiner la direction du player
```

- Ajouter une fonction **mouseMoved()** qui permet de déplacer la direction du player en fonction de la position de la souris;

```js
function mouseMoved() {
	const dx = mouseX - player.pos.x
	const dy = mouseY - player.pos.y
	player.ang = 0 // TODO: remplacer 0 par l'angle du player en utilisant Math.atan2()
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/oNoXLRQ)

---

### Calculer et afficher les intersections

Nous allons:

- Créer une fonction **intersect()** qui prend en paramètre deux segments et qui retourne les coordonnées du point d'intersection entre ces deux segments. Si les segments sont parallèles, la fonction retourne null.

```js
const intersect = ([p1, p2], [p3, p4]) => {
	// TODO: retourner l'intersection entre les deux segments
	// https://en.wikipedia.org/wiki/Line%E2%80%93line_intersection
}
```

- Créer une foncton **rayCast()** qui prend en paramètre un rayon et qui retourne l'ensemble des segments formés par les intersections entre le rayon depuis la position du player et les murs présents dans la scène.

```js
const rayCast = (ray) => {
	// TODO: on mappe les intersections entre le rayon et les murs
	// TODO: on filtre les intersections nulles
	// TODO: on mappe les intersections pour obtenir les segments entre le player et les intersections
	// return walls.map(???).filter(???).map(???)
}
```

- Créer une fonction **drawIntersectionRay()** qui prend en paramètre un segment [position du player, intersection] et qui dessine ce segment. Rappelons que la fonction précedente retourne une liste de ce type de segments.

```js
const drawIntersectionRay = (segment) => {
	// TODO: dessiner le segment
}
```

- Modifier la fonction **draw()** pour dessiner les intersections;

```js
const intersectionRays = rayCast(ray)
// TODO: dessiner les segments obtenus avec la fonction drawIntersectionRay()
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/dyZoXBa)

---

### Filtrer les intersections

Nous allons désormais ne considérer que les intersections les plus proches du player et uniquement celles qui sont dans le champ de vision du player. Pour cela, nous allons:

- Modifier la fonction **intersect()** pour ne considérer que les intersections qui sont dans le champ de vision du player;

```js
// TODO: modifier la fonction intersect()
```

- Modifier la fonction **rayCast()** pour ne considérer que les intersections les plus proches du player;

```js
// TODO: modifier la fonction rayCast()
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/Exbjgye)

---

### Envoyer plusieurs rayons

Nous allons maintenant envoyer plusieurs rayons et afficher les intersections obtenues. Pour cela, nous allons:

- Modifier la fonction **draw()** pour envoyer plusieurs rayons et afficher les intersections obtenues;

```js
const rays = []
rays.push(getRay(0 - 0.5, 10))
rays.push(getRay(0 + 0.0, 10))
rays.push(getRay(0 + 0.5, 10))
// TODO: envoyer plusieurs rayons et afficher les intersections obtenues
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/bGYRLjM)

---

### Rendre variable le nombre de rayons

Nous allons maintenant rendre variable le nombre de rayons envoyés. Pour cela, nous allons:

- Ajouter une propriété **fov** à la variable **player** qui contiendra le champ de vision du player;

```js
const player = {
	pos: null,
	ang: null,
	fov: (50 * Math.PI) / 180,
}
```

- Ajouter une variable **arcs** qui contiendra la liste des angles des rayons;

```js
const arcs = []
```

- Modifier la fonction **initialize()** pour initialiser la liste des angles des rayons;

```js
arcs.length = 3
for (let i = 0; i < arcs.length; i++) {
	arcs[i] = arcs.length === 1 ? 0.5 : i / (arcs.length - 1)
}
```

- Modifier la fonction **draw()** pour prendre en compte le champ de vision du player et la liste des angles des rayons;

```js
const rays = arcs.map((arc) => {
	return getRay(map(arc, 0, 1, 0 - player.fov / 2, 0 + player.fov / 2), 1)
})
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/abVwqaz)

---

### Splitter l'écran en deux

Nous allons maintenant splitter l'écran en deux. La partie haute affichera la scène en vue du dessus et la partie basse affichera la scène en vue FPS. Pour cela, nous allons:

- Modifier la fonction **initialize()** ;

```js
screen.h = height / 2
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/abVwqRO)

---

### Afficher la scène en vue FPS

Nous allons afficher les murs en choisissant la couleur en fonction de la distance entre le player et le mur. Pour cela, nous allons:

- Modifier la fonction **mouseMoved()** afin de bouger la position du player au lieu de la direction;

```js
function mouseMoved() {
	player.pos.set(mouseX, mouseY)
}
```

- Modifier la fonction **setup()**;

```js
function setup() {
	createCanvas(windowWidth, windowHeight)
	rectMode(CENTER) // On utilise le mode CENTER pour dessiner les rectangles
	initialize()
}
```

- Modifier la fonction **draw()** pour dessiner la scène en vue FPS. On ajoute ce code en fin de fonction;

```js
// On remappe l'origine de la scène pour les tracés suivants
translate(0, screen.h)

// On rend la scène en vue FPS
noStroke()
const rectW = screen.w / intersections.length
const rectH = screen.h

intersections.forEach((intersection, i) => {
	// Pour chaque intersection, on calcule la couleur du mur qui sera plus claire si l'intersection est plus proche
	const dist = intersection[2]
	const wallC = min(255, (screen.w * screen.h) / (30 * dist))

	const wallH = rectH

	fill(wallC)
	rect(i * rectW + rectW / 2, rectH / 2, rectW, wallH)
})
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/zYPzRMY)

---

### Amélioration du rendu

Nous allons maintenant améliorer le rendu. Pour cela, nous allons:

- Modifier la fonction **draw()** pour gommer l'effet fish-eye;

```js
const intersections = rays
	.map((ray) => rayCast(ray))
	// Nous devons ajouter l'angle de l'arc afin de corriger l'effet fish-eye
	.map((intersection, i) => [...intersection, map(arcs[i], 0, 1, 0 - player.fov / 2, 0 + player.fov / 2)])
```

```js
const dist = intersection[2]
const wallC = min(255, (screen.w * screen.h) / (30 * dist))

// calculate the wall height with fish-eye correction
const arc = intersection[3]
const wallH = min(rectH, (rectH * 100) / (dist * cos(arc)))

fill(wallC)
rect(i * rectW + rectW / 2, rectH / 2, rectW, wallH)
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/RwjgQqg)

---

# Chargement de la carte

Nous allons maintenant charger la carte. Pour cela, nous allons:

- Modifier la fonction **initialize()** pour charger la carte;

```js
const w = screen.w / 20
const h = screen.h / 15

const segments = [
	[0, 0, 20, 0, 20, 15, 0, 15],
	[1, 1, 6, 1, 6, 3, 8, 3, 8, 1, 19, 1, 19, 14, 6, 14, 6, 9, 3, 9, 3, 10, 5, 10, 5, 14, 1, 14, 1, 6, 3, 6, 3, 7, 4, 7, 4, 4, 1, 4],
	[8, 6, 10, 6, 10, 7, 8, 7],
	[9, 9, 10, 9, 10, 10, 9, 10],
	[11, 3, 17, 3, 17, 12, 11, 12, 11, 8, 12, 8, 12, 11, 16, 11, 16, 4, 12, 4, 12, 5, 11, 5],
	[13, 6, 14, 6, 14, 10, 13, 10],
]

for (const segment of segments) {
	for (let i = 0; i < segment.length; i += 2) {
		const j = (i == 0 ? segment.length : i) - 2
		walls.push([createVector(segment[j + 0] * w, segment[j + 1] * h), createVector(segment[i + 0] * w, segment[i + 1] * h)])
	}
}
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/zYPzRyY)

---

### Dessiner le sol et le ciel

Nous allons maintenant dessiner le sol et le ciel. Pour cela, nous allons:

- Modifier la fonction **draw()** pour dessiner le sol et le ciel;

```js
// Juste avant de dessiner les murs, on dessine le sol et le ciel

// Le ciel est bleu
fill(90, 90, 180)
rect(width / 2, (1 * rectH) / 4, width, rectH / 2)

// Le sol est vert
fill(80, 160, 80)
rect(width / 2, (3 * rectH) / 4, width, rectH / 2)
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/ExbXQrj)

---

### Affinage des contrôles du player et gestion des collisions

Nous allons maintenant affiner les contrôles du player et gérer les collisions. Pour cela, nous allons:

- Créer la fonction **playerMove()** qui deplace le joueur dans une direction et d'une distance. La fonction devra gérer les collisions;

```js
const playerMove = (ang, len) => {
	// TODO: gérer les collisions
	player.pos.add(p5.Vector.fromAngle(player.ang + ang).mult(len))
}
```

- Créer la fonction **handleKeyboardEvents()**;

```js
const handleKeyboardEvents = () => {
	if (keyIsPressed) {
		switch (keyCode) {
			case LEFT_ARROW:
			case 65: // A key
				// turn left
				player.ang -= 0.05
				break
			case RIGHT_ARROW:
			case 68: // D key
				// turn right
				player.ang += 0.05
				break
			case UP_ARROW:
			case 87: // W key
				// move forward
				playerMove(0.0 * Math.PI, 7)
				break
			case DOWN_ARROW:
			case 83: // S key
				// move backward
				playerMove(1.0 * Math.PI, 7)
				break
			case 81: // Q key
				// strafe left
				playerMove(1.5 * Math.PI, 7)
				break
			case 69: // E key
				// strafe right
				playerMove(0.5 * Math.PI, 7)
				break
			case 189: // - key
				// reduce the player's field of view
				player.fov -= 0.05
				break
			case 187: // + key
				// increase the player's field of view
				player.fov += 0.05
				break
			default:
				console.log(keyCode)
				break
		}
	}
}
```

- Modifier la fonction **draw()** pour appeler la fonction **handleKeyboardEvents()** à la fin de la fonction;

```js
handleKeyboardEvents()
```

- Modifier la fonction **mouseMoved()**;

```js
function mouseMoved() {
	// Changer la direction du joueur en bougeant la souris sur l'axe X
	player.ang = mouseX / 100
}
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/GROEQzO)
