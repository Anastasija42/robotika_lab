# Laboratorija za robotiku - OnePose projekat
![ss](https://github.com/Anastasija42/robotika_lab/assets/101294691/cb57c01c-14ab-4fb9-8f5c-98fe300ee5ab)
![ss2](https://github.com/Anastasija42/robotika_lab/assets/101294691/06500d21-fca8-4eef-b5de-a69f4e70a4a5)

## OnePose: One-Shot Object Pose Estimation without CAD Models
> [Instalacija OnePose](https://github.com/zju3dv/OnePose) 
<br/>

## Pravljenje sopstvenog dataseta
> [Instalacija OnePoseDatasetCollector](https://github.com/siatheindochinese/OnePoseDatasetCollector)

Pokrenuti `arucoboard.py` za generisanje aruco `.png` fajla. Fajl se čuva u `out/aruco/`. Dužina kvadrata (`sl`) i dužina markera (`ml`) mogu se podesiti po potrebi. Primer:
```
python3 arucoboard.py --config sl200_ml120
```
Pokrenuti `detect_arucoboard_realsense.py`, umesto `empty` staviti ime predmeta.
```
python3 detect_arucoboard_realsense.py --config sl200_ml120 --name empty
```
Kontrole:
1. 'w' i 's': menjanje visine centra 
2. 'e' i 'd': menjanje širine bounding box-a po x-osi
3. 'r' i 'f': menjanje širine bounding box-a po y-osi
4. 't' i 'g': menjanje visine bounding box-a
5. 'spacebar': čuvanje podataka (RGB, poses, intrinsics, bounding box).
6. 'q': zaustaviti program
<br/>
Dataset je sačuvan u `out/datasets/`.

## Predobrada
Napraviti Structure-from-Motion model na osnovu snimljenog dataset-a. 
U `sfm_spp_spg_sample.yaml` fajlu proveriti da li je `redo` vrednost postavljena na True. Postaviti `scan_data_dir` na željeni direktorijum (podrazumevano je ${work_dir}/data/onepose_datasets/sample_data). Pod `data_dir` navesti sve objekte koje želite da obradite. Dobijeni modeli čuvaju se u `/data/sfm_model/{}`.
```
python run.py +preprocess=sfm_spp_spg_sample.yaml
```
<br/>

## Pre pokretanja bilo koje skripte
Pokrenuti sledece komande kako bi skripta prepoznala DeepLM modul.
```
export PYTHONPATH=$PYTHONPATH:/home/ana/Desktop:/home/ana/Desktop/DeepLM/build:/home/ana/Desktop/DeepLM
export TORCH_USE_RTLD_GLOBAL=YES
```

## Detekcija objekata u kvazi-realnom vremenu - sekvencijalno
U `test_demo.yaml` podesiti broj objekata koje očekujemo i vrstu objekata koje potencijalno očekujemo.  
<br/>
Ukoliko je cilj estimacija pozicije i orijentacije samo jednog objekta, pokrenuti skriptu `one.py`. Potrebno je smestiti odgovarajuci SFM model na adresi `/OnePose-main/data/demo/sfm_model` , u sledecem obliku:
```
|--- /sfm_model
|       |--- outputs_superpoint_superglue 
|       |--- box3d_corners.txt
```
`box3d_corners.txt` prekopirati iz `/home/ana/Desktop/OnePoseDatasetCollector/out/datasets/zeljeni_objekat/box3d_corners.txt`.
Dok se skripta izvrsava, `rgb` prozor ce prikazivati estimaciju. Pritiskom na dugme `q` program se zavrsava, a video prenosa se cuva na adresi `/home/ana/Desktop/real_time_seq/`. Obzirom da se ne obradjuje svaki frame u "real-time" pristupu, sam snimak ce biti ubrzan. Svaka estimacija pojedinacnih frame-ova se cuva na adresi `/home/ana/Desktop/real_time_seq/1`. Ovo je najjednostavniji pristup.
<br/>

Napomene za mene:
Fajl `one.py` i `two.py` rade sekvencijalno, `two.py` može i više istih objekata odjednom.
<br/> Pokrenuti fajl, iskace prozor, cuva se video na kraju.

## Detekcija objekata u kvazi-realnom vremenu - paralelni pristup
U `test_demo.yaml` podesiti broj objekata koje očekujemo i vrstu objekata koje potencijalno očekujemo.
<br/> Pokrenuti fajl, iskace prozor, cuva se video na kraju.

Napomena za mene:
Fajl `proba2.py` je paralelni pristup, treba doraditi za kopije iste vrste. svuda dodati distancu.


## Detekcija objekata u snimku
Sledećom komandom se otvara program pomoću kojeg možemo snimiti snimak. Format sačuvanog videa je `.bag` fajl, potrebno je izdvojiti frame-ove pomoću `rs-convert` komande. Pokrenuti fajl `rename.py` da bi se izvršilo preimenovanje frame-ova, potrebno je podesiti `folder_path` sa željenim direktorijumom.
```
realsense-viewer
rs-convert -i nas_snimak.bag -p zeljeni_direktorijum/prefiks -c
python3 rename.py
```
U `test_demo.yaml` podesiti broj objekata koje očekujemo i vrstu objekata koje potencijalno očekujemo.
<br/> 
Pokrenuti dobar fajl.
<br/>
Video je sacuvan negde.

Napomena za mene:
pogledati
`drugi_fajl_paralelno.py`
`drugi_fajl.py`
`video.py`
`video_bez_uticaja.py`

## Ako nešto ne radi, cimati anastasiju :)
