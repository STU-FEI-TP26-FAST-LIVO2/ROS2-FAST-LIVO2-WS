# Postup na build workspace-u
Po úspešnej inštalácii [FAST-LIVO2 package-u](https://github.com/STU-FEI-TP26-FAST-LIVO2/ROS2-FAST-LIVO2) a všetkých potrebných prerekvizít je potrebné v správnom poradí buildnúť celý workspace. Postup je nasledovný

## Build Livoxu

 Ako prvé buildujeme Livox, je nutné nachádzať sa v adresári `livox_ros_driver2`
```
cd src/livox_ros_driver2
./build.sh humble
```
#### Upozornenie
Build pravdepodobne z časti zlyhá, ale to nie je problém, a preto je nutné pokračovať ďalej a nezaoberať sa touto chybou.


Ďalší problém, ktorý môže nastať, je chýbajúci prístup k buildu, dá sa to jednoducho opraviť
```
chmod +x build.sh
```

## Build WS
Po "úspešnom" buildnutí Livoxu sa vrátime do `koreňového adresára workspace-u` a buildneme celý workspace
```
cd ~/ROS2-FAST-LIVO2-WS
colcon build --symlink-install
```

V prípade Jetsona zlyháva build jedného z Vikit balíkov (nateraz to nie je dôležité, zaoberať sa tým budeme v prípade, že balík bude potrebný), a teda je lepšie buildovať takto
```
cd ~/ROS2-FAST-LIVO2-WS
colcon build --symlink-install --packages-ignore vikit_py
```

Po úspešnom buildnutí WS je potrebné ho source-núť, source-ujeme takto
```
source install/setup.bash
```
Na Jetsone by malo stačiť otvoriť nové okno terminálu. 

#### Upozornenie č. 2
Tento postup je len dočasný, nakoľko s Livoxom pracovať nebudeme.
