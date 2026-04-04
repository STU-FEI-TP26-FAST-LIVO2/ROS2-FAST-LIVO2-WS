# 1. Postup na build workspace-u
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

# 2. Postup na spustenie rosbagu a FAST-LIVO2
Do ľubovoľného, respektíve novo vytvoreného priečinku (vo WS ale mimo src) je potrebné [stiahnuť dataset](https://hilti-challenge.com/dataset-2022). Odporúčam `Exp14 Basement 2`. V tomto priečinku spravíme konverziu z ROS1 rosbagu na ROS2 rosbag
```
pip install rosbags
cd priecinok_kde_je_stiahnuty_dataset
rosbags-convert --src exp14_basement_2.bag --dst exp14_basement_2
```
Po konverzií sa vytvorí nový priečinok s konvertovaným datasetom (konverzia chvíľu trvá, je bez výpisu čiže treba len počkať kým príkaz zbehne). Ďalej je v priečinku potrebné skontrolovať `metadata.yaml` a  **všetky** výskyty `offered_qos_profiles` zmeniť takto

```diff
- offered_qos_profiles: []
+ offered_qos_profiles: ''

```
Ďalej môžeme spustiť rosbag a FAST-LIVO2 v tomto poradí
```
cd priecinok_kde_je_stiahnuty_dataset #priecinok nad vytvoreným exp14_basement_2
ros2 bag play exp14_basement_2 --clock
```
Rosbag sa hneď spustí, dá sa zastaviť aj spomaliť (tieto informácie sa po spustení vypíšu). V ďalšom terminále spustíme FAST-LIVO2
```
ros2 launch fast_livo hesaihilti.launch.py use_rviz:=True