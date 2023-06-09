# éclairage d'évier automatique
<img src="https://raw.githubusercontent.com/fred2541/Lumiere-Evier/master/Images/IMG_20230408_071218.jpg" height="300">

## L'intérêt:
Si, comme moi, votre évier se trouve dans un coin sombre avec un meuble juste au-dessus et la lumière principale de votre cuisine derrière vous, il y a de fortes chances que votre évier soit sombre de jour comme de nuit.
## Solution:
Dans un premier temps, j'ai installé un ruban de LED sous le meuble haut afin de pouvoir éclairer correctement l'évier. Comme je ne voulais pas d'interrupteur pour allumer ou éteindre cette lumière, j'ai installé un Sonoff Basic R2 pour contrôler l'alimentation du transformateur 220V des LED.

Ce Sonoff R2 est reprogrammé pour fonctionner avec ESP Easy, et j'utilisais Google Home pour lui dire d'allumer ou d'éteindre la lumière. Jusqu'au jour où cela ne me convenait plus. C'est rigolo, mais c'est quand même une contrainte de parler pour allumer une lumière.

J'ai donc connecté un capteur d'ultrasons sur le Sonoff R2 pour mesurer la distance entre le meuble et ce qui se trouve devant. Le principe est simple : quand on arrive devant l'évier, les ultrasons que notre corps refléchit permettent d'obtenir une distance, et quand cette distance est inférieure à 100 cm, le Sonoff R2 active l'alimentation électrique grâce à son relais.

# Matériel nécessaire:
- SONOFF BASIC R2
- Ultrasonic Sensor HC-SR04
- Ruban de Led avec transfo 220V


# Le code:
```
on System#Boot do
let,1,0
endon

on SR04#Distance>5 do
   gpio,1,0 // reset after each measure
   if [SR04#Distance]<100  // Ok
   Let,1,[VAR#1]+1
     if [var#1]=2  // If present on two cycle of measure 
       gpio,12,1 // Relais ON
       let,1,0 // reset presence var to 0
       timerSet,1,0 // reset Timer1
       timerSet,1,30 // Start timer1 for 30 sec
     endif  // End present two cycle
   else
   let,1,0 // reset presence var to 0
     endif
   endif
 endon

On Rules#Timer=1 do
   gpio,12,0  // Relais off on Timer end

on SR04#Distance=0 do  // if Distance=0 --> bug, reset capteur
   gpio,1,0
 endon
 ```
