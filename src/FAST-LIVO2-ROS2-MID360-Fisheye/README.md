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

V prípade Jetsona zlyháva build jedného z Vikit balíkov, a teda je lepšie buildovať takto
```
cd ~/ROS2-FAST-LIVO2-WS
colcon build --symlink-install --packages-ignore vikit_py
```

Po úspešnom buildnutí WS je potrebné ho source-núť, source-ujeme takto
```
source install/setup.bash
```
Na Jetsone by malo stačiť otvoriť nové okno terminálu. 

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
```

 # 3. Konfiguračné súbory
V adresári `src/FAST-LIVO2-ROS2-MID360-Fisheye/config` sa nachádzajú konfiguračné súbory s príponou *.yaml*, v ktorých nastavujeme parametre pre dostupný hardvér. Takéto súbory sú dva, jeden je pre všeobecné nastavenia FAST-LIVO2 a druhý je pre nastavenie kamery. Ak by sme chceli spustiť FAST-LIVO2 s iným hardvérom (napríklad v prípade spustenia online dostupných rosbagov), je potrebné tieto súbory upraviť a zahrnúť do launch súboru. Parametre, ktoré je pri zmene hardvéru nutné upraviť, sú takéto
#### Konfiguračný súbor pre FAST-LIVO2
Topic-y pre hardvér:
```
lid_topic: "/lidar_points"
imu_topic: "/imu"
img_topic: "/basler/image_raw"
```
Zmena z LIVO na LIO a naopak:
```
img_en: 1 # 1 - LIVO, 0 - LIO
```
Real time alebo simulácia:
```
use_sim_time: false # false - real time, true - simulácia
```
Extrinzická kalibrácia IMU → LiDAR:
```
extrinsic_T: [0.0, 0.0, -0.06954819845]
extrinsic_R: [-1., 0., 0., 0., 1., 0., 0., 0., -1.]
```
Extrinzická kalibrácia kamera → LiDAR:
```
Rcl: [-1.0, 0.0, 0.0,
     0.0, 0.0, -1.0,
     0.0, -1.0, 0.0]
Pcl: [0.0, -0.09590, -0.003819]
```
#### Konfiguračný súbor pre kameru
V prípade kamery je potrebné upraviť všetky parametre, ktoré sa v súbore nachádzajú. Tieto parametre sú typ kamery (napríklad *Pinhole*), rozlíšenie, ohniskové vzdialenosti *fx* a *fy*, a hlavný bod *cx* a *cy*. Posledným parametrom sú distorzné koeficienty.