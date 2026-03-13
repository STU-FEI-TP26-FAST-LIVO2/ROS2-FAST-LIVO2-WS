# Postup na build workspace-u
Po úspešnej inštalácii [FAST-LIVO2 package-u](https://github.com/STU-FEI-TP26-FAST-LIVO2/ROS2-FAST-LIVO2) a všetkých potrebných prerekvizít je potrebné v správnom poradí buildnúť celý workspace. Postup je nasledovný:

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
cd ~/fast_ws
colcon build --symlink-install
```
Po úspešnom buildnutí WS je potrebné ho source-núť, postačuje **otvoriť nové okno terminálu**, pokiaľ by to ale nebolo vyhovujúce, source-ujeme takto
```
source install/setup.bash
```

#### Upozornenie č. 2
Tento postup je len dočasný, nakoľko s Livoxom pracovať nebudeme.
