
**Weather Analysis**

All statements are valid for the end of March 2018. During another time of a year significant changes may occur.
1. There is significantly more cities located in a Northern Hemisphere. Temperature vs Latitude plot shows a direct correlation between a distance from the equator with a temperature. The farther the city is located from the equator, the colder weather is there. Some outliers are possible on low latitudes - high mountains cities, where the temperatures are lower than the average for this latitude.
2. The correlation between Latitude and Humidity is not so obvious, but there is a trend that Humidity is 30% higher on the equator (from -20° S to 10° N). Also, the less humid regions can be found from 10° N to 50° N.
3. There is no dependence observed between Latitude and Cloudiness. There is a slight increase in wind speed as a distance from the equator increases. Also, it can be mentioned, that winds are insignificantly stronger in a Northern Hemisphere, probably because the inhabited territory is much larger.


```python
import requests
import pandas as pd
import random
import openweathermapy.core as owm
import matplotlib.pyplot as plt
from matplotlib import style
import datetime
import seaborn as sns
from citipy import citipy
from pprint import pprint
from config import api_key
from itertools import cycle
```

**Create a list of random cities**

#### test code that needs to be fixed
cities = {}

def onemoreloop():
    return citylistcreator()

def citylistcreator():
    for x in range(500):
        coord = ((round(random.uniform(-90.00, 90.00),2)),(round(random.uniform(-180.00, 180.00),2)))
        citydata = citipy.nearest_city(*coord)
        city = citydata.city_name
        if city not in cities:
            cities[city] = coord
        else:
            try:
                return onemoreloop()
            except(RecursionError):
                return onemoreloop()

citylistcreator()
print(len(cities))

**From 3000 cities we can get about 1000 unique cities**


```python
cities = {}

for x in range(3000):
    coord = ((round(random.uniform(-90.00, 90.00),2)),(round(random.uniform(-180.00, 180.00),2)))
    citydata = citipy.nearest_city(*coord)
    city = citydata.city_name
    #if city not in cities: Not necessary, because of using dictionary
        #cities[city] = coord
    #else:
        #pass
    cities[city] = coord # Coordinates are added to be able to look up based on them, for debugging

print(len(cities))
```

    993


**Retrieve cities data from openweathermap using Requests**


```python
url = 'http://api.openweathermap.org/data/2.5/weather?'
units = 'imperial'

all_requests_data = {}
counter = 1 # City number counter
absent_cities_req = 0 # Counter for the cities that are not in the openweather database

for city in cities:
    
    pure_city_name = city
    city = requests.utils.quote(city, safe='') # Make a city name readable (percent-encoded)
    ind_url = f'{url}appid={api_key}&units={units}&q={city}'
    # The line below is blocked, not to show the api_key
    #print(f'Now processing: city number {counter}, name {pure_city_name}\n{ind_url}')
    print(f'Now processing: city number {counter}, name {pure_city_name}')
    data_json = requests.get(ind_url).json()
    
    try:
        all_requests_data[data_json['name']] = [data_json['sys']['country'], data_json['coord']['lat'],
                                                data_json['coord']['lon'],
                                              data_json['main']['temp'], data_json['main']['humidity'],
                                              data_json['clouds']['all'], data_json['wind']['speed'], data_json['dt']]
    except(KeyError):
        print(f'There is no city {pure_city_name} in openweathermap database')
        absent_cities_req +=1
        continue
    counter += 1
    
print(f'{absent_cities_req} cities were skipped')
print(f'{len(all_requests_data)} cities data was captured')
```

    Now processing: city number 1, name mar del plata
    Now processing: city number 2, name cape town
    Now processing: city number 3, name tsihombe
    There is no city tsihombe in openweathermap database
    Now processing: city number 3, name rikitea
    Now processing: city number 4, name port elizabeth
    Now processing: city number 5, name grand gaube
    Now processing: city number 6, name soroti
    Now processing: city number 7, name bredasdorp
    Now processing: city number 8, name tuktoyaktuk
    Now processing: city number 9, name rio grande
    Now processing: city number 10, name jacareacanga
    Now processing: city number 11, name puerto ayora
    Now processing: city number 12, name bandarbeyla
    Now processing: city number 13, name phan thiet
    Now processing: city number 14, name jamestown
    Now processing: city number 15, name port-gentil
    Now processing: city number 16, name punta arenas
    Now processing: city number 17, name toliary
    There is no city toliary in openweathermap database
    Now processing: city number 17, name port alfred
    Now processing: city number 18, name new norfolk
    Now processing: city number 19, name arraial do cabo
    Now processing: city number 20, name paamiut
    Now processing: city number 21, name saint george
    Now processing: city number 22, name attawapiskat
    There is no city attawapiskat in openweathermap database
    Now processing: city number 22, name chokurdakh
    Now processing: city number 23, name ilulissat
    Now processing: city number 24, name flinders
    Now processing: city number 25, name albany
    Now processing: city number 26, name korla
    There is no city korla in openweathermap database
    Now processing: city number 26, name mandera
    Now processing: city number 27, name mount juliet
    Now processing: city number 28, name linjiang
    Now processing: city number 29, name souillac
    Now processing: city number 30, name chapais
    Now processing: city number 31, name tiksi
    Now processing: city number 32, name gornopravdinsk
    Now processing: city number 33, name aklavik
    Now processing: city number 34, name hilo
    Now processing: city number 35, name khatanga
    Now processing: city number 36, name lebu
    Now processing: city number 37, name illoqqortoormiut
    There is no city illoqqortoormiut in openweathermap database
    Now processing: city number 37, name nurota
    Now processing: city number 38, name sandpoint
    Now processing: city number 39, name kangaatsiaq
    Now processing: city number 40, name barrow
    Now processing: city number 41, name severo-kurilsk
    Now processing: city number 42, name faya
    Now processing: city number 43, name belushya guba
    There is no city belushya guba in openweathermap database
    Now processing: city number 43, name vaini
    Now processing: city number 44, name eureka
    Now processing: city number 45, name talcahuano
    Now processing: city number 46, name nanortalik
    Now processing: city number 47, name sentyabrskiy
    There is no city sentyabrskiy in openweathermap database
    Now processing: city number 47, name tame
    Now processing: city number 48, name tezu
    Now processing: city number 49, name vestmannaeyjar
    Now processing: city number 50, name bluff
    Now processing: city number 51, name klaksvik
    Now processing: city number 52, name butaritari
    Now processing: city number 53, name xingyi
    Now processing: city number 54, name sao filipe
    Now processing: city number 55, name kapoeta
    There is no city kapoeta in openweathermap database
    Now processing: city number 55, name upernavik
    Now processing: city number 56, name mys shmidta
    There is no city mys shmidta in openweathermap database
    Now processing: city number 56, name beira
    Now processing: city number 57, name avera
    Now processing: city number 58, name dire
    Now processing: city number 59, name atuona
    Now processing: city number 60, name lavras
    Now processing: city number 61, name waingapu
    Now processing: city number 62, name ushuaia
    Now processing: city number 63, name nemuro
    Now processing: city number 64, name cockburn town
    Now processing: city number 65, name barentsburg
    There is no city barentsburg in openweathermap database
    Now processing: city number 65, name mizan teferi
    Now processing: city number 66, name locri
    Now processing: city number 67, name chagda
    There is no city chagda in openweathermap database
    Now processing: city number 67, name walvis bay
    Now processing: city number 68, name hermanus
    Now processing: city number 69, name sao joao da barra
    Now processing: city number 70, name gazli
    Now processing: city number 71, name mataura
    Now processing: city number 72, name havelock
    Now processing: city number 73, name brewster
    Now processing: city number 74, name kruisfontein
    Now processing: city number 75, name leningradskiy
    Now processing: city number 76, name college
    Now processing: city number 77, name avarua
    Now processing: city number 78, name galiakot
    Now processing: city number 79, name busselton
    Now processing: city number 80, name daru
    Now processing: city number 81, name marsaxlokk
    Now processing: city number 82, name tangshan
    Now processing: city number 83, name mariental
    Now processing: city number 84, name san patricio
    Now processing: city number 85, name taolanaro
    There is no city taolanaro in openweathermap database
    Now processing: city number 85, name kapaa
    Now processing: city number 86, name luau
    Now processing: city number 87, name hofn
    Now processing: city number 88, name coquimbo
    Now processing: city number 89, name ipixuna
    Now processing: city number 90, name airai
    Now processing: city number 91, name tuy hoa
    Now processing: city number 92, name mudyuga
    There is no city mudyuga in openweathermap database
    Now processing: city number 92, name esperance
    Now processing: city number 93, name uvat
    Now processing: city number 94, name gizo
    Now processing: city number 95, name cayenne
    Now processing: city number 96, name saint-denis
    Now processing: city number 97, name riyadh
    Now processing: city number 98, name sitka
    Now processing: city number 99, name ruatoria
    There is no city ruatoria in openweathermap database
    Now processing: city number 99, name matveyevka
    Now processing: city number 100, name raha
    Now processing: city number 101, name hearst
    Now processing: city number 102, name petatlan
    Now processing: city number 103, name saldanha
    Now processing: city number 104, name saskylakh
    Now processing: city number 105, name zarand
    Now processing: city number 106, name sibolga
    Now processing: city number 107, name amapa
    Now processing: city number 108, name faanui
    Now processing: city number 109, name hobart
    Now processing: city number 110, name mazarron
    Now processing: city number 111, name vigeland
    Now processing: city number 112, name sehithwa
    Now processing: city number 113, name llanes
    Now processing: city number 114, name port macquarie
    Now processing: city number 115, name heze
    Now processing: city number 116, name grand river south east
    There is no city grand river south east in openweathermap database
    Now processing: city number 116, name san quintin
    Now processing: city number 117, name tasiilaq
    Now processing: city number 118, name karasjok
    Now processing: city number 119, name minbu
    Now processing: city number 120, name mecca
    Now processing: city number 121, name lagoa
    Now processing: city number 122, name carnarvon
    Now processing: city number 123, name ponta do sol
    Now processing: city number 124, name katsuura
    Now processing: city number 125, name serowe
    Now processing: city number 126, name dikson
    Now processing: city number 127, name nome
    Now processing: city number 128, name codrington
    Now processing: city number 129, name lao cai
    Now processing: city number 130, name qeshm
    Now processing: city number 131, name wajir
    Now processing: city number 132, name christchurch
    Now processing: city number 133, name torbay
    Now processing: city number 134, name kavieng
    Now processing: city number 135, name northam
    Now processing: city number 136, name provideniya
    Now processing: city number 137, name bambous virieux
    Now processing: city number 138, name norman wells
    Now processing: city number 139, name castro
    Now processing: city number 140, name mackay
    Now processing: city number 141, name east millcreek
    Now processing: city number 142, name port hardy
    Now processing: city number 143, name thompson
    Now processing: city number 144, name sao desiderio
    Now processing: city number 145, name mosetse
    Now processing: city number 146, name rocha
    Now processing: city number 147, name georgetown
    Now processing: city number 148, name lorengau
    Now processing: city number 149, name pevek
    Now processing: city number 150, name mwinilunga
    Now processing: city number 151, name egvekinot
    Now processing: city number 152, name vila franca do campo
    Now processing: city number 153, name kizhinga
    Now processing: city number 154, name komsomolskiy
    Now processing: city number 155, name dargaville
    Now processing: city number 156, name andreapol
    Now processing: city number 157, name conde
    Now processing: city number 158, name hauterive
    Now processing: city number 159, name zhengjiatun
    Now processing: city number 160, name kajaani
    Now processing: city number 161, name bougouni
    Now processing: city number 162, name khawhai
    Now processing: city number 163, name yellowknife
    Now processing: city number 164, name imeni poliny osipenko
    Now processing: city number 165, name charters towers
    Now processing: city number 166, name preobrazheniye
    Now processing: city number 167, name bilma
    Now processing: city number 168, name tanete
    Now processing: city number 169, name mazagao
    Now processing: city number 170, name punta cardon
    Now processing: city number 171, name cherskiy
    Now processing: city number 172, name japura
    Now processing: city number 173, name vaitupu
    There is no city vaitupu in openweathermap database
    Now processing: city number 173, name kapuskasing
    Now processing: city number 174, name wexford
    Now processing: city number 175, name qunduz
    There is no city qunduz in openweathermap database
    Now processing: city number 175, name atambua
    Now processing: city number 176, name dongsheng
    Now processing: city number 177, name kodiak
    Now processing: city number 178, name svarstad
    Now processing: city number 179, name broome
    Now processing: city number 180, name wewak
    Now processing: city number 181, name ribeira grande
    Now processing: city number 182, name puerto ayacucho
    Now processing: city number 183, name salalah
    Now processing: city number 184, name longyearbyen
    Now processing: city number 185, name kovernino
    Now processing: city number 186, name grimari
    There is no city grimari in openweathermap database
    Now processing: city number 186, name linhares
    Now processing: city number 187, name nizhneyansk
    There is no city nizhneyansk in openweathermap database
    Now processing: city number 187, name necochea
    Now processing: city number 188, name vanderhoof
    Now processing: city number 189, name dongning
    Now processing: city number 190, name qaanaaq
    Now processing: city number 191, name tabulbah
    There is no city tabulbah in openweathermap database
    Now processing: city number 191, name praia da vitoria
    Now processing: city number 192, name fairbanks
    Now processing: city number 193, name qui nhon
    There is no city qui nhon in openweathermap database
    Now processing: city number 193, name bethel
    Now processing: city number 194, name jiayuguan
    Now processing: city number 195, name anori
    Now processing: city number 196, name lompoc
    Now processing: city number 197, name san jose
    Now processing: city number 198, name klamath falls
    Now processing: city number 199, name itarema
    Now processing: city number 200, name gbarnga
    Now processing: city number 201, name casambalangan
    Now processing: city number 202, name lalomanu
    There is no city lalomanu in openweathermap database
    Now processing: city number 202, name borama
    There is no city borama in openweathermap database
    Now processing: city number 202, name hualmay
    Now processing: city number 203, name east london
    Now processing: city number 204, name sturgeon bay
    Now processing: city number 205, name valparaiso
    Now processing: city number 206, name sinnamary
    Now processing: city number 207, name atar
    Now processing: city number 208, name rawson
    Now processing: city number 209, name evensk
    Now processing: city number 210, name pemba
    Now processing: city number 211, name achisay
    There is no city achisay in openweathermap database
    Now processing: city number 211, name erdenet
    Now processing: city number 212, name pak chong
    Now processing: city number 213, name khorlovo
    Now processing: city number 214, name sovetskiy
    Now processing: city number 215, name ishinomaki
    Now processing: city number 216, name clyde river
    Now processing: city number 217, name saint-philippe
    Now processing: city number 218, name smithers
    Now processing: city number 219, name puerto cortes
    There is no city puerto cortes in openweathermap database
    Now processing: city number 219, name khani
    Now processing: city number 220, name colares
    Now processing: city number 221, name victoria
    Now processing: city number 222, name lata
    Now processing: city number 223, name peterhead
    Now processing: city number 224, name andenes
    There is no city andenes in openweathermap database
    Now processing: city number 224, name kandla
    Now processing: city number 225, name saint anthony
    Now processing: city number 226, name tromso
    Now processing: city number 227, name tonj
    There is no city tonj in openweathermap database
    Now processing: city number 227, name myitkyina
    Now processing: city number 228, name bushehr
    Now processing: city number 229, name salinopolis
    Now processing: city number 230, name kristiinankaupunki
    There is no city kristiinankaupunki in openweathermap database
    Now processing: city number 230, name abatskoye
    Now processing: city number 231, name zhigansk
    Now processing: city number 232, name madang
    Now processing: city number 233, name biscarrosse
    Now processing: city number 234, name goderich
    Now processing: city number 235, name tilichiki
    Now processing: city number 236, name hithadhoo
    Now processing: city number 237, name kahului
    Now processing: city number 238, name ostrovnoy
    Now processing: city number 239, name amderma
    There is no city amderma in openweathermap database
    Now processing: city number 239, name iqaluit
    Now processing: city number 240, name kinsale
    Now processing: city number 241, name contamana
    Now processing: city number 242, name mmabatho
    Now processing: city number 243, name quelimane
    Now processing: city number 244, name san juan
    Now processing: city number 245, name isangel
    Now processing: city number 246, name kaitangata
    Now processing: city number 247, name vilyuysk
    Now processing: city number 248, name araguacu
    There is no city araguacu in openweathermap database
    Now processing: city number 248, name tuatapere
    Now processing: city number 249, name marrakesh
    Now processing: city number 250, name ancud
    Now processing: city number 251, name waipawa
    Now processing: city number 252, name kodino
    Now processing: city number 253, name harstad
    Now processing: city number 254, name moose factory
    Now processing: city number 255, name labuhan
    Now processing: city number 256, name mashivka
    Now processing: city number 257, name koumac
    Now processing: city number 258, name lakatoro
    Now processing: city number 259, name texistepec
    Now processing: city number 260, name leh
    Now processing: city number 261, name ust-kulom
    Now processing: city number 262, name henties bay
    Now processing: city number 263, name mahebourg
    Now processing: city number 264, name sanwer
    Now processing: city number 265, name praia
    Now processing: city number 266, name lakes entrance
    Now processing: city number 267, name poum
    Now processing: city number 268, name vidisha
    Now processing: city number 269, name djibo
    Now processing: city number 270, name miyazu
    Now processing: city number 271, name kendari
    Now processing: city number 272, name ha giang
    Now processing: city number 273, name cuite
    Now processing: city number 274, name pedernales
    Now processing: city number 275, name nikolskoye
    Now processing: city number 276, name male
    Now processing: city number 277, name sitrah
    Now processing: city number 278, name asosa
    Now processing: city number 279, name baoding
    Now processing: city number 280, name dunedin
    Now processing: city number 281, name traverse city
    Now processing: city number 282, name carbonia
    Now processing: city number 283, name tongliao
    Now processing: city number 284, name rongcheng
    Now processing: city number 285, name samut sakhon
    Now processing: city number 286, name guerrero negro
    Now processing: city number 287, name bengkulu
    There is no city bengkulu in openweathermap database
    Now processing: city number 287, name ambilobe
    Now processing: city number 288, name gat
    Now processing: city number 289, name mrirt
    There is no city mrirt in openweathermap database
    Now processing: city number 289, name kiama
    Now processing: city number 290, name moussoro
    Now processing: city number 291, name mehamn
    Now processing: city number 292, name gorontalo
    Now processing: city number 293, name shimoda
    Now processing: city number 294, name chuy
    Now processing: city number 295, name puerto escondido
    Now processing: city number 296, name tigzirt
    Now processing: city number 297, name hasaki
    Now processing: city number 298, name aripuana
    Now processing: city number 299, name burnie
    Now processing: city number 300, name acarau
    There is no city acarau in openweathermap database
    Now processing: city number 300, name talnakh
    Now processing: city number 301, name bathsheba
    Now processing: city number 302, name palmer
    Now processing: city number 303, name sakakah
    There is no city sakakah in openweathermap database
    Now processing: city number 303, name hambantota
    Now processing: city number 304, name tumannyy
    There is no city tumannyy in openweathermap database
    Now processing: city number 304, name west bay
    Now processing: city number 305, name lazaro cardenas
    Now processing: city number 306, name vao
    Now processing: city number 307, name det udom
    Now processing: city number 308, name los llanos de aridane
    Now processing: city number 309, name ahmadpur east
    Now processing: city number 310, name paradwip
    There is no city paradwip in openweathermap database
    Now processing: city number 310, name port alberni
    Now processing: city number 311, name tacuati
    Now processing: city number 312, name luwuk
    Now processing: city number 313, name mirnyy
    Now processing: city number 314, name cabo san lucas
    Now processing: city number 315, name matadi
    Now processing: city number 316, name seymchan
    Now processing: city number 317, name havre-saint-pierre
    Now processing: city number 318, name menongue
    Now processing: city number 319, name wukari
    Now processing: city number 320, name muros
    Now processing: city number 321, name grand forks
    Now processing: city number 322, name zhangye
    Now processing: city number 323, name ostroh
    Now processing: city number 324, name maceio
    Now processing: city number 325, name yulara
    Now processing: city number 326, name khandyga
    Now processing: city number 327, name tunduru
    There is no city tunduru in openweathermap database
    Now processing: city number 327, name ishige
    Now processing: city number 328, name ucluelet
    Now processing: city number 329, name venancio aires
    Now processing: city number 330, name grindavik
    Now processing: city number 331, name mishelevka
    Now processing: city number 332, name kalmunai
    Now processing: city number 333, name palana
    Now processing: city number 334, name shingu
    Now processing: city number 335, name biak
    Now processing: city number 336, name porto walter
    Now processing: city number 337, name maneadero
    There is no city maneadero in openweathermap database
    Now processing: city number 337, name hoquiam
    Now processing: city number 338, name diapaga
    Now processing: city number 339, name yarada
    Now processing: city number 340, name jala
    Now processing: city number 341, name tucuma
    There is no city tucuma in openweathermap database
    Now processing: city number 341, name sorvag
    There is no city sorvag in openweathermap database
    Now processing: city number 341, name flin flon
    Now processing: city number 342, name antalaha
    Now processing: city number 343, name batagay
    Now processing: city number 344, name namatanai
    Now processing: city number 345, name constitucion
    Now processing: city number 346, name brokopondo
    Now processing: city number 347, name tuggurt
    There is no city tuggurt in openweathermap database
    Now processing: city number 347, name nezhinka
    Now processing: city number 348, name chicama
    Now processing: city number 349, name prattville
    Now processing: city number 350, name villa bruzual
    Now processing: city number 351, name caravelas
    Now processing: city number 352, name nerchinskiy zavod
    Now processing: city number 353, name abu zabad
    Now processing: city number 354, name tidore
    There is no city tidore in openweathermap database
    Now processing: city number 354, name pontes e lacerda
    Now processing: city number 355, name yankton
    Now processing: city number 356, name kalamare
    Now processing: city number 357, name tura
    Now processing: city number 358, name udachnyy
    Now processing: city number 359, name north bend
    Now processing: city number 360, name nishihara
    Now processing: city number 361, name majene
    Now processing: city number 362, name katangli
    Now processing: city number 363, name mahalapye
    Now processing: city number 364, name mount gambier
    Now processing: city number 365, name davila
    Now processing: city number 366, name vostok
    Now processing: city number 367, name jizan
    Now processing: city number 368, name kangundo
    Now processing: city number 369, name manzhouli
    Now processing: city number 370, name vikulovo
    Now processing: city number 371, name comodoro rivadavia
    Now processing: city number 372, name sesheke
    Now processing: city number 373, name sangmelima
    Now processing: city number 374, name istok
    Now processing: city number 375, name coffs harbour
    Now processing: city number 376, name visby
    Now processing: city number 377, name high rock
    Now processing: city number 378, name biltine
    Now processing: city number 379, name baykit
    Now processing: city number 380, name lewistown
    Now processing: city number 381, name teknaf
    Now processing: city number 382, name agadez
    Now processing: city number 383, name emirdag
    Now processing: city number 384, name iwanai
    Now processing: city number 385, name halifax
    Now processing: city number 386, name iracoubo
    Now processing: city number 387, name valdosta
    Now processing: city number 388, name muravlenko
    Now processing: city number 389, name vila velha
    Now processing: city number 390, name sataua
    There is no city sataua in openweathermap database
    Now processing: city number 390, name oda
    Now processing: city number 391, name gua musang
    Now processing: city number 392, name sambava
    Now processing: city number 393, name dingle
    Now processing: city number 394, name ketchikan
    Now processing: city number 395, name lufilufi
    Now processing: city number 396, name saint-leu
    Now processing: city number 397, name mangai
    Now processing: city number 398, name mocambique
    There is no city mocambique in openweathermap database
    Now processing: city number 398, name mikkeli
    Now processing: city number 399, name husavik
    Now processing: city number 400, name nioro
    Now processing: city number 401, name grand-santi
    Now processing: city number 402, name palabuhanratu
    There is no city palabuhanratu in openweathermap database
    Now processing: city number 402, name shakawe
    Now processing: city number 403, name pangnirtung
    Now processing: city number 404, name genhe
    Now processing: city number 405, name saint-pierre
    Now processing: city number 406, name quilmana
    Now processing: city number 407, name veraval
    Now processing: city number 408, name mahibadhoo
    Now processing: city number 409, name belmonte
    Now processing: city number 410, name hassleholm
    Now processing: city number 411, name cacoal
    Now processing: city number 412, name naze
    Now processing: city number 413, name yar-sale
    Now processing: city number 414, name georgiyevka
    Now processing: city number 415, name churapcha
    Now processing: city number 416, name taltal
    Now processing: city number 417, name jahanabad
    Now processing: city number 418, name touros
    Now processing: city number 419, name tara
    Now processing: city number 420, name camapua
    Now processing: city number 421, name lugovskiy
    There is no city lugovskiy in openweathermap database
    Now processing: city number 421, name stornoway
    There is no city stornoway in openweathermap database
    Now processing: city number 421, name santa cruz cabralia
    Now processing: city number 422, name tateyama
    Now processing: city number 423, name altamira
    Now processing: city number 424, name harper
    Now processing: city number 425, name umzimvubu
    There is no city umzimvubu in openweathermap database
    Now processing: city number 425, name opichen
    Now processing: city number 426, name entre rios
    Now processing: city number 427, name jegunovce
    Now processing: city number 428, name matamoros
    Now processing: city number 429, name asasa
    Now processing: city number 430, name kainantu
    Now processing: city number 431, name uaua
    Now processing: city number 432, name kununurra
    Now processing: city number 433, name port lincoln
    Now processing: city number 434, name carahue
    Now processing: city number 435, name khartoum
    Now processing: city number 436, name sotouboua
    Now processing: city number 437, name xining
    Now processing: city number 438, name busayra
    Now processing: city number 439, name champerico
    Now processing: city number 440, name zolotinka
    There is no city zolotinka in openweathermap database
    Now processing: city number 440, name mandiana
    Now processing: city number 441, name kudahuvadhoo
    Now processing: city number 442, name kvarkeno
    Now processing: city number 443, name stara vyzhivka
    Now processing: city number 444, name bonavista
    Now processing: city number 445, name borogontsy
    Now processing: city number 446, name inhuma
    Now processing: city number 447, name kimbe
    Now processing: city number 448, name rassvet
    Now processing: city number 449, name chalmette
    Now processing: city number 450, name gravdal
    Now processing: city number 451, name la orilla
    Now processing: city number 452, name sampit
    Now processing: city number 453, name vanimo
    Now processing: city number 454, name general roca
    Now processing: city number 455, name punta alta
    Now processing: city number 456, name biograd na moru
    Now processing: city number 457, name richards bay
    Now processing: city number 458, name skovorodino
    Now processing: city number 459, name olafsvik
    There is no city olafsvik in openweathermap database
    Now processing: city number 459, name pandan
    Now processing: city number 460, name jatai
    Now processing: city number 461, name atasu
    Now processing: city number 462, name aykhal
    Now processing: city number 463, name batticaloa
    Now processing: city number 464, name innisfail
    Now processing: city number 465, name half moon bay
    Now processing: city number 466, name takoradi
    Now processing: city number 467, name morgan city
    Now processing: city number 468, name berlevag
    Now processing: city number 469, name sola
    Now processing: city number 470, name bubaque
    Now processing: city number 471, name skjervoy
    Now processing: city number 472, name highland park
    Now processing: city number 473, name odda
    Now processing: city number 474, name onega
    Now processing: city number 475, name bandundu
    Now processing: city number 476, name belyy yar
    Now processing: city number 477, name narsaq
    Now processing: city number 478, name lere
    Now processing: city number 479, name mancio lima
    There is no city mancio lima in openweathermap database
    Now processing: city number 479, name werda
    Now processing: city number 480, name kutum
    Now processing: city number 481, name kavaratti
    Now processing: city number 482, name umm lajj
    Now processing: city number 483, name san cristobal
    Now processing: city number 484, name ardakan
    Now processing: city number 485, name chandur bazar
    Now processing: city number 486, name vondrozo
    Now processing: city number 487, name srednekolymsk
    Now processing: city number 488, name bartoszyce
    Now processing: city number 489, name sunds
    Now processing: city number 490, name ondjiva
    Now processing: city number 491, name bireun
    Now processing: city number 492, name oranjestad
    Now processing: city number 493, name puri
    Now processing: city number 494, name dahod
    Now processing: city number 495, name kamaishi
    Now processing: city number 496, name novoagansk
    Now processing: city number 497, name muisne
    Now processing: city number 498, name novosergiyevka
    Now processing: city number 499, name burns lake
    Now processing: city number 500, name haibowan
    There is no city haibowan in openweathermap database
    Now processing: city number 500, name lerwick
    Now processing: city number 501, name pacific grove
    Now processing: city number 502, name cidreira
    Now processing: city number 503, name gedo
    Now processing: city number 504, name castro-urdiales
    Now processing: city number 505, name karratha
    Now processing: city number 506, name houma
    Now processing: city number 507, name el porvenir
    Now processing: city number 508, name bismil
    Now processing: city number 509, name darovskoy
    Now processing: city number 510, name totness
    Now processing: city number 511, name vila
    Now processing: city number 512, name plettenberg bay
    Now processing: city number 513, name rosarno
    Now processing: city number 514, name suntar
    Now processing: city number 515, name eenhana
    Now processing: city number 516, name lunenburg
    Now processing: city number 517, name beringovskiy
    Now processing: city number 518, name lavrentiya
    Now processing: city number 519, name grenville
    Now processing: city number 520, name proletariy
    Now processing: city number 521, name colorado
    Now processing: city number 522, name raymond
    Now processing: city number 523, name taunggyi
    Now processing: city number 524, name fernie
    Now processing: city number 525, name huarmey
    Now processing: city number 526, name margate
    Now processing: city number 527, name kawalu
    Now processing: city number 528, name linfen
    Now processing: city number 529, name skibbereen
    Now processing: city number 530, name hamilton
    Now processing: city number 531, name yarensk
    Now processing: city number 532, name bulgan
    Now processing: city number 533, name vardo
    Now processing: city number 534, name falavarjan
    Now processing: city number 535, name felanitx
    Now processing: city number 536, name nkhata bay
    Now processing: city number 537, name malm
    Now processing: city number 538, name korgen
    Now processing: city number 539, name samusu
    There is no city samusu in openweathermap database
    Now processing: city number 539, name utete
    Now processing: city number 540, name austin
    Now processing: city number 541, name burica
    There is no city burica in openweathermap database
    Now processing: city number 541, name port-cartier
    Now processing: city number 542, name willowmore
    Now processing: city number 543, name butler
    Now processing: city number 544, name liwonde
    Now processing: city number 545, name quatre cocos
    Now processing: city number 546, name bayir
    Now processing: city number 547, name soyo
    Now processing: city number 548, name hyeres
    Now processing: city number 549, name lerum
    Now processing: city number 550, name dolores
    Now processing: city number 551, name agnibilekrou
    Now processing: city number 552, name abrau-dyurso
    Now processing: city number 553, name laguna
    Now processing: city number 554, name mvangue
    Now processing: city number 555, name marcona
    There is no city marcona in openweathermap database
    Now processing: city number 555, name gorshechnoye
    Now processing: city number 556, name pilar
    Now processing: city number 557, name mount isa
    Now processing: city number 558, name bonnyville
    Now processing: city number 559, name manggar
    Now processing: city number 560, name brae
    Now processing: city number 561, name iguala
    Now processing: city number 562, name razole
    Now processing: city number 563, name qaqortoq
    Now processing: city number 564, name urumqi
    There is no city urumqi in openweathermap database
    Now processing: city number 564, name luderitz
    Now processing: city number 565, name wahran
    There is no city wahran in openweathermap database
    Now processing: city number 565, name wanning
    Now processing: city number 566, name tiarei
    Now processing: city number 567, name banff
    Now processing: city number 568, name toppenish
    Now processing: city number 569, name padang
    Now processing: city number 570, name harrisonville
    Now processing: city number 571, name sorland
    Now processing: city number 572, name sangar
    Now processing: city number 573, name velur
    Now processing: city number 574, name menzelinsk
    Now processing: city number 575, name mapastepec
    Now processing: city number 576, name pathein
    Now processing: city number 577, name lichuan
    Now processing: city number 578, name tessalit
    Now processing: city number 579, name pauini
    Now processing: city number 580, name masvingo
    Now processing: city number 581, name tabiauea
    There is no city tabiauea in openweathermap database
    Now processing: city number 581, name kysyl-syr
    Now processing: city number 582, name chara
    Now processing: city number 583, name buala
    Now processing: city number 584, name maniitsoq
    Now processing: city number 585, name patacamaya
    Now processing: city number 586, name portland
    Now processing: city number 587, name stokmarknes
    Now processing: city number 588, name oriximina
    Now processing: city number 589, name etalle
    Now processing: city number 590, name banda aceh
    Now processing: city number 591, name lago da pedra
    There is no city lago da pedra in openweathermap database
    Now processing: city number 591, name mogadishu
    Now processing: city number 592, name alenquer
    Now processing: city number 593, name paita
    Now processing: city number 594, name mizdah
    Now processing: city number 595, name aasiaat
    Now processing: city number 596, name aksu
    Now processing: city number 597, name lakhisarai
    Now processing: city number 598, name safita
    Now processing: city number 599, name puerto baquerizo moreno
    Now processing: city number 600, name waslala
    Now processing: city number 601, name hawick
    Now processing: city number 602, name abalak
    Now processing: city number 603, name seoul
    Now processing: city number 604, name dondaicha
    Now processing: city number 605, name capelinha
    Now processing: city number 606, name garowe
    Now processing: city number 607, name coruripe
    Now processing: city number 608, name roebourne
    Now processing: city number 609, name kasongo-lunda
    Now processing: city number 610, name jasper
    Now processing: city number 611, name kacergine
    There is no city kacergine in openweathermap database
    Now processing: city number 611, name whitianga
    Now processing: city number 612, name kayes
    Now processing: city number 613, name turka
    Now processing: city number 614, name selaphum
    Now processing: city number 615, name ruteng
    Now processing: city number 616, name jerome
    Now processing: city number 617, name hargeysa
    Now processing: city number 618, name high level
    Now processing: city number 619, name marsh harbour
    Now processing: city number 620, name maputo
    Now processing: city number 621, name okahandja
    Now processing: city number 622, name carutapera
    Now processing: city number 623, name kasungu
    Now processing: city number 624, name fort nelson
    Now processing: city number 625, name dudinka
    Now processing: city number 626, name coihueco
    Now processing: city number 627, name pingliang
    Now processing: city number 628, name bilibino
    Now processing: city number 629, name samana
    Now processing: city number 630, name ostrov
    Now processing: city number 631, name choma
    Now processing: city number 632, name olga
    Now processing: city number 633, name geraldton
    Now processing: city number 634, name mago
    Now processing: city number 635, name kargopol
    Now processing: city number 636, name floro
    Now processing: city number 637, name hornepayne
    Now processing: city number 638, name el rosario
    Now processing: city number 639, name cap malheureux
    Now processing: city number 640, name lac du bonnet
    Now processing: city number 641, name reno
    Now processing: city number 642, name syedove
    Now processing: city number 643, name batagay-alyta
    Now processing: city number 644, name arieseni
    Now processing: city number 645, name mnogovershinnyy
    Now processing: city number 646, name sabang
    Now processing: city number 647, name hihifo
    There is no city hihifo in openweathermap database
    Now processing: city number 647, name viedma
    Now processing: city number 648, name yumen
    Now processing: city number 649, name manta
    Now processing: city number 650, name kon tum
    Now processing: city number 651, name verkhoyansk
    Now processing: city number 652, name umba
    Now processing: city number 653, name opuwo
    Now processing: city number 654, name ulaangom
    Now processing: city number 655, name gamba
    Now processing: city number 656, name whyalla
    Now processing: city number 657, name qinzhou
    Now processing: city number 658, name sinop
    Now processing: city number 659, name sobolevo
    Now processing: city number 660, name verkhnyaya toyma
    Now processing: city number 661, name jackson
    Now processing: city number 662, name celestun
    Now processing: city number 663, name catalao
    Now processing: city number 664, name bucerias
    Now processing: city number 665, name fortuna
    Now processing: city number 666, name karkaralinsk
    There is no city karkaralinsk in openweathermap database
    Now processing: city number 666, name katobu
    Now processing: city number 667, name uberlandia
    Now processing: city number 668, name itaueira
    Now processing: city number 669, name kolokani
    Now processing: city number 670, name kuche
    There is no city kuche in openweathermap database
    Now processing: city number 670, name najran
    Now processing: city number 671, name kloulklubed
    Now processing: city number 672, name azimur
    There is no city azimur in openweathermap database
    Now processing: city number 672, name dzhusaly
    There is no city dzhusaly in openweathermap database
    Now processing: city number 672, name la ronge
    Now processing: city number 673, name kashi
    There is no city kashi in openweathermap database
    Now processing: city number 673, name hare bay
    Now processing: city number 674, name cumaribo
    There is no city cumaribo in openweathermap database
    Now processing: city number 674, name san carlos
    Now processing: city number 675, name samalaeulu
    There is no city samalaeulu in openweathermap database
    Now processing: city number 675, name adrar
    Now processing: city number 676, name beidao
    Now processing: city number 677, name mandla
    Now processing: city number 678, name marawi
    Now processing: city number 679, name saint-georges
    Now processing: city number 680, name koraput
    Now processing: city number 681, name micheweni
    Now processing: city number 682, name uthal
    Now processing: city number 683, name dali
    Now processing: city number 684, name havoysund
    Now processing: city number 685, name whitehorse
    Now processing: city number 686, name batemans bay
    Now processing: city number 687, name mandalgovi
    Now processing: city number 688, name boueni
    Now processing: city number 689, name petropavlovsk-kamchatskiy
    Now processing: city number 690, name boddam
    Now processing: city number 691, name manokwari
    Now processing: city number 692, name ottawa
    Now processing: city number 693, name aranos
    Now processing: city number 694, name asyut
    Now processing: city number 695, name roald
    Now processing: city number 696, name danane
    Now processing: city number 697, name shiraz
    Now processing: city number 698, name kholm
    Now processing: city number 699, name voykove
    Now processing: city number 700, name xiaoshi
    Now processing: city number 701, name hienghene
    Now processing: city number 702, name straumen
    Now processing: city number 703, name coihaique
    Now processing: city number 704, name tsuyama
    Now processing: city number 705, name pristina
    There is no city pristina in openweathermap database
    Now processing: city number 705, name vestmanna
    Now processing: city number 706, name guilin
    Now processing: city number 707, name mersing
    Now processing: city number 708, name kadykchan
    There is no city kadykchan in openweathermap database
    Now processing: city number 708, name milkovo
    Now processing: city number 709, name port augusta
    Now processing: city number 710, name salina cruz
    Now processing: city number 711, name asau
    There is no city asau in openweathermap database
    Now processing: city number 711, name sioux lookout
    Now processing: city number 712, name hinton
    Now processing: city number 713, name khatra
    Now processing: city number 714, name yuzhnouralsk
    Now processing: city number 715, name te anau
    Now processing: city number 716, name launceston
    Now processing: city number 717, name ishigaki
    Now processing: city number 718, name salisbury
    Now processing: city number 719, name suixi
    Now processing: city number 720, name berbera
    There is no city berbera in openweathermap database
    Now processing: city number 720, name aljezur
    Now processing: city number 721, name malakal
    There is no city malakal in openweathermap database
    Now processing: city number 721, name pasni
    Now processing: city number 722, name la rioja
    Now processing: city number 723, name arlit
    Now processing: city number 724, name hervey bay
    Now processing: city number 725, name kirakira
    Now processing: city number 726, name lucapa
    Now processing: city number 727, name xuddur
    Now processing: city number 728, name codroipo
    Now processing: city number 729, name imamoglu
    Now processing: city number 730, name lithakia
    Now processing: city number 731, name fleetwood
    Now processing: city number 732, name nyurba
    Now processing: city number 733, name nuevo progreso
    Now processing: city number 734, name saint-francois
    Now processing: city number 735, name pasighat
    Now processing: city number 736, name santa lucia
    Now processing: city number 737, name assiniboia
    Now processing: city number 738, name owando
    Now processing: city number 739, name san pedro de uraba
    Now processing: city number 740, name jiangyou
    Now processing: city number 741, name otjimbingwe
    Now processing: city number 742, name lasa
    Now processing: city number 743, name lephepe
    There is no city lephepe in openweathermap database
    Now processing: city number 743, name kirkwood
    Now processing: city number 744, name bima
    Now processing: city number 745, name quang ngai
    Now processing: city number 746, name belaya gora
    Now processing: city number 747, name vanavara
    Now processing: city number 748, name coro
    Now processing: city number 749, name pierre
    Now processing: city number 750, name sungaipenuh
    Now processing: city number 751, name san lorenzo
    Now processing: city number 752, name strezhevoy
    Now processing: city number 753, name acajutla
    Now processing: city number 754, name meulaboh
    Now processing: city number 755, name algiers
    Now processing: city number 756, name bahia blanca
    Now processing: city number 757, name pisco
    Now processing: city number 758, name iskateley
    Now processing: city number 759, name cerro largo
    Now processing: city number 760, name liminangcong
    There is no city liminangcong in openweathermap database
    Now processing: city number 760, name palu
    Now processing: city number 761, name alta floresta
    Now processing: city number 762, name thyboron
    Now processing: city number 763, name tautira
    Now processing: city number 764, name ossora
    Now processing: city number 765, name khorramshahr
    Now processing: city number 766, name gari
    Now processing: city number 767, name binga
    Now processing: city number 768, name diffa
    Now processing: city number 769, name pimentel
    Now processing: city number 770, name manadhoo
    Now processing: city number 771, name colquechaca
    Now processing: city number 772, name auxerre
    Now processing: city number 773, name ponta delgada
    Now processing: city number 774, name salym
    Now processing: city number 775, name barawe
    There is no city barawe in openweathermap database
    Now processing: city number 775, name khor
    Now processing: city number 776, name conceicao do araguaia
    Now processing: city number 777, name gamboma
    Now processing: city number 778, name grootfontein
    Now processing: city number 779, name la plata
    Now processing: city number 780, name tagaytay
    Now processing: city number 781, name pascagoula
    Now processing: city number 782, name kerki
    There is no city kerki in openweathermap database
    Now processing: city number 782, name birin
    Now processing: city number 783, name progreso
    Now processing: city number 784, name falealupo
    There is no city falealupo in openweathermap database
    Now processing: city number 784, name nuuk
    Now processing: city number 785, name dromolaxia
    Now processing: city number 786, name dicabisagan
    Now processing: city number 787, name yarovoye
    Now processing: city number 788, name sao felix do xingu
    Now processing: city number 789, name mullaitivu
    There is no city mullaitivu in openweathermap database
    Now processing: city number 789, name lima
    Now processing: city number 790, name kieta
    Now processing: city number 791, name amambai
    Now processing: city number 792, name verkhoturye
    Now processing: city number 793, name manjacaze
    Now processing: city number 794, name shubarkuduk
    Now processing: city number 795, name makakilo city
    Now processing: city number 796, name malanje
    Now processing: city number 797, name samarai
    Now processing: city number 798, name krasnyy chikoy
    Now processing: city number 799, name sabzevar
    Now processing: city number 800, name spasskoye
    Now processing: city number 801, name exu
    Now processing: city number 802, name sofiysk
    There is no city sofiysk in openweathermap database
    Now processing: city number 802, name koroni
    Now processing: city number 803, name ygatimi
    Now processing: city number 804, name muscat
    Now processing: city number 805, name jining
    Now processing: city number 806, name luganville
    Now processing: city number 807, name solnechnyy
    Now processing: city number 808, name uyuni
    Now processing: city number 809, name banapur
    Now processing: city number 810, name puerto madryn
    Now processing: city number 811, name santa cruz
    Now processing: city number 812, name tekeli
    Now processing: city number 813, name corinto
    Now processing: city number 814, name mwene-ditu
    Now processing: city number 815, name trairi
    Now processing: city number 816, name puerto el triunfo
    Now processing: city number 817, name turukhansk
    Now processing: city number 818, name tobermory
    Now processing: city number 819, name winnemucca
    Now processing: city number 820, name alindao
    Now processing: city number 821, name ouegoa
    Now processing: city number 822, name san juan de la vega
    Now processing: city number 823, name yatou
    Now processing: city number 824, name lamas
    Now processing: city number 825, name kodinsk
    Now processing: city number 826, name koutsouras
    Now processing: city number 827, name dehui
    Now processing: city number 828, name bargur
    Now processing: city number 829, name kamenka
    Now processing: city number 830, name san carlos de bariloche
    Now processing: city number 831, name coloso
    Now processing: city number 832, name taoudenni
    Now processing: city number 833, name ekibastuz
    Now processing: city number 834, name black river
    Now processing: city number 835, name sedhiou
    Now processing: city number 836, name hammerfest
    Now processing: city number 837, name san rafael
    Now processing: city number 838, name haines junction
    Now processing: city number 839, name nuevo emperador
    Now processing: city number 840, name malgrat de mar
    Now processing: city number 841, name buraydah
    Now processing: city number 842, name jalu
    Now processing: city number 843, name erzin
    Now processing: city number 844, name madimba
    Now processing: city number 845, name mangan
    Now processing: city number 846, name ulladulla
    Now processing: city number 847, name adre
    Now processing: city number 848, name sahrak
    There is no city sahrak in openweathermap database
    Now processing: city number 848, name cururupu
    Now processing: city number 849, name imperia
    Now processing: city number 850, name sainte-anne-des-monts
    Now processing: city number 851, name sisimiut
    Now processing: city number 852, name namibe
    Now processing: city number 853, name nizhniy odes
    Now processing: city number 854, name bihariganj
    Now processing: city number 855, name astoria
    Now processing: city number 856, name sabya
    Now processing: city number 857, name caledon
    Now processing: city number 858, name tagusao
    Now processing: city number 859, name sylacauga
    Now processing: city number 860, name itoman
    Now processing: city number 861, name alugan
    Now processing: city number 862, name copiapo
    Now processing: city number 863, name naqadeh
    Now processing: city number 864, name wufeng
    Now processing: city number 865, name parana
    Now processing: city number 866, name messina
    Now processing: city number 867, name yerbogachen
    Now processing: city number 868, name bhum
    Now processing: city number 869, name poso
    Now processing: city number 870, name hayden
    Now processing: city number 871, name araouane
    Now processing: city number 872, name tongchuan
    Now processing: city number 873, name terenos
    Now processing: city number 874, name phangnga
    Now processing: city number 875, name porto santo
    There is no city porto santo in openweathermap database
    Now processing: city number 875, name raudeberg
    Now processing: city number 876, name mozarlandia
    Now processing: city number 877, name pojuca
    Now processing: city number 878, name tres arroyos
    Now processing: city number 879, name cochrane
    Now processing: city number 880, name coos bay
    Now processing: city number 881, name alice springs
    Now processing: city number 882, name sibu
    Now processing: city number 883, name olinda
    Now processing: city number 884, name mormugao
    There is no city mormugao in openweathermap database
    Now processing: city number 884, name mpanda
    Now processing: city number 885, name sosua
    Now processing: city number 886, name micomeseng
    Now processing: city number 887, name pyay
    Now processing: city number 888, name macaboboni
    There is no city macaboboni in openweathermap database
    Now processing: city number 888, name novovasylivka
    Now processing: city number 889, name telemaco borba
    Now processing: city number 890, name the valley
    Now processing: city number 891, name konibodom
    Now processing: city number 892, name miyang
    Now processing: city number 893, name eirunepe
    Now processing: city number 894, name tumut
    Now processing: city number 895, name nyeri
    Now processing: city number 896, name kem
    Now processing: city number 897, name anadyr
    Now processing: city number 898, name tombouctou
    Now processing: city number 899, name oktyabrskoye
    Now processing: city number 900, name panzhihua
    Now processing: city number 901, name kikwit
    Now processing: city number 902, name lira
    Now processing: city number 903, name invermere
    Now processing: city number 904, name shubarshi
    Now processing: city number 905, name camacha
    Now processing: city number 906, name nizwa
    Now processing: city number 907, name warmbad
    Now processing: city number 908, name jacmel
    Now processing: city number 909, name kamenskoye
    There is no city kamenskoye in openweathermap database
    85 cities were skipped
    908 cities data was captured


**Create a DataFrame based on Requests data**


```python
dframe_json = pd.DataFrame(all_requests_data)
final_req_df = dframe_json.transpose()
final_req_df.columns = ['Country', 'Latitude', 'Longitude', 'Temperature(F)', 'Humidity(%)', 'Cloudiness(%)', 'Wind Speed(MpH)',
                        'Date']
final_req_df.to_csv('Output/Weather_requests.csv')
print(final_req_df.count())
final_req_df.head()
```

    Country            908
    Latitude           908
    Longitude          908
    Temperature(F)     908
    Humidity(%)        908
    Cloudiness(%)      908
    Wind Speed(MpH)    908
    Date               908
    dtype: int64





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Country</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Temperature(F)</th>
      <th>Humidity(%)</th>
      <th>Cloudiness(%)</th>
      <th>Wind Speed(MpH)</th>
      <th>Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Aasiaat</th>
      <td>GL</td>
      <td>68.71</td>
      <td>-52.87</td>
      <td>17.73</td>
      <td>83</td>
      <td>12</td>
      <td>2.59</td>
      <td>1522710866</td>
    </tr>
    <tr>
      <th>Abalak</th>
      <td>NE</td>
      <td>15.46</td>
      <td>6.28</td>
      <td>68.4</td>
      <td>47</td>
      <td>12</td>
      <td>3.6</td>
      <td>1522711437</td>
    </tr>
    <tr>
      <th>Abatskoye</th>
      <td>RU</td>
      <td>56.29</td>
      <td>70.46</td>
      <td>16.56</td>
      <td>83</td>
      <td>8</td>
      <td>10.54</td>
      <td>1522711353</td>
    </tr>
    <tr>
      <th>Abrau-Dyurso</th>
      <td>RU</td>
      <td>44.7</td>
      <td>37.6</td>
      <td>46.4</td>
      <td>87</td>
      <td>56</td>
      <td>6.71</td>
      <td>1522708200</td>
    </tr>
    <tr>
      <th>Abu Zabad</th>
      <td>SD</td>
      <td>12.35</td>
      <td>29.25</td>
      <td>65.66</td>
      <td>28</td>
      <td>0</td>
      <td>6.17</td>
      <td>1522711374</td>
    </tr>
  </tbody>
</table>
</div>



**Retrieve cities data from openweathermap using openweathermapy**

**Here and below I use metric system because Farenheits sound like Sanskrit for me**


```python
settings = {"units": "metric", "appid": api_key} 
```


```python
import urllib

all_owm_data = []
absent_cities_owm = 0

for city in cities:
    try:
        all_owm_data.append(owm.get_current(city, **settings))
    except urllib.error.HTTPError:
        print(f'There is no city {city} in openweathermap database')
        absent_cities_owm += 1
        continue
        
print(f'{absent_cities_owm} cities were skipped')
print(f'{len(all_owm_data)} cities data was captured')
```

    There is no city tsihombe in openweathermap database
    There is no city toliary in openweathermap database
    There is no city attawapiskat in openweathermap database
    There is no city korla in openweathermap database
    There is no city illoqqortoormiut in openweathermap database
    There is no city belushya guba in openweathermap database
    There is no city sentyabrskiy in openweathermap database
    There is no city kapoeta in openweathermap database
    There is no city mys shmidta in openweathermap database
    There is no city barentsburg in openweathermap database
    There is no city chagda in openweathermap database
    There is no city taolanaro in openweathermap database
    There is no city mudyuga in openweathermap database
    There is no city ruatoria in openweathermap database
    There is no city grand river south east in openweathermap database
    There is no city vaitupu in openweathermap database
    There is no city qunduz in openweathermap database
    There is no city grimari in openweathermap database
    There is no city nizhneyansk in openweathermap database
    There is no city tabulbah in openweathermap database
    There is no city qui nhon in openweathermap database
    There is no city lalomanu in openweathermap database
    There is no city borama in openweathermap database
    There is no city achisay in openweathermap database
    There is no city puerto cortes in openweathermap database
    There is no city andenes in openweathermap database
    There is no city tonj in openweathermap database
    There is no city kristiinankaupunki in openweathermap database
    There is no city amderma in openweathermap database
    There is no city araguacu in openweathermap database
    There is no city bengkulu in openweathermap database
    There is no city mrirt in openweathermap database
    There is no city acarau in openweathermap database
    There is no city sakakah in openweathermap database
    There is no city tumannyy in openweathermap database
    There is no city paradwip in openweathermap database
    There is no city tunduru in openweathermap database
    There is no city maneadero in openweathermap database
    There is no city tucuma in openweathermap database
    There is no city sorvag in openweathermap database
    There is no city tuggurt in openweathermap database
    There is no city tidore in openweathermap database
    There is no city sataua in openweathermap database
    There is no city mocambique in openweathermap database
    There is no city palabuhanratu in openweathermap database
    There is no city lugovskiy in openweathermap database
    There is no city stornoway in openweathermap database
    There is no city umzimvubu in openweathermap database
    There is no city zolotinka in openweathermap database
    There is no city olafsvik in openweathermap database
    There is no city mancio lima in openweathermap database
    There is no city haibowan in openweathermap database
    There is no city samusu in openweathermap database
    There is no city burica in openweathermap database
    There is no city marcona in openweathermap database
    There is no city urumqi in openweathermap database
    There is no city wahran in openweathermap database
    There is no city tabiauea in openweathermap database
    There is no city lago da pedra in openweathermap database
    There is no city kacergine in openweathermap database
    There is no city hihifo in openweathermap database
    There is no city karkaralinsk in openweathermap database
    There is no city kuche in openweathermap database
    There is no city azimur in openweathermap database
    There is no city dzhusaly in openweathermap database
    There is no city kashi in openweathermap database
    There is no city cumaribo in openweathermap database
    There is no city samalaeulu in openweathermap database
    There is no city pristina in openweathermap database
    There is no city kadykchan in openweathermap database
    There is no city asau in openweathermap database
    There is no city berbera in openweathermap database
    There is no city malakal in openweathermap database
    There is no city lephepe in openweathermap database
    There is no city liminangcong in openweathermap database
    There is no city barawe in openweathermap database
    There is no city kerki in openweathermap database
    There is no city falealupo in openweathermap database
    There is no city mullaitivu in openweathermap database
    There is no city sofiysk in openweathermap database
    There is no city sahrak in openweathermap database
    There is no city porto santo in openweathermap database
    There is no city mormugao in openweathermap database
    There is no city macaboboni in openweathermap database
    There is no city kamenskoye in openweathermap database
    85 cities were skipped
    908 cities data was captured


**Create a DataFrame based on OWM data**


```python
search = ['name', 'sys.country', 'coord.lat', 'coord.lon', 'main.temp','main.humidity', 'clouds.all', 'wind.speed', 'dt']
extracted_data = [city(*search) for city in all_owm_data]

final_owm_df = pd.DataFrame(extracted_data)
final_owm_df.columns = ['Name','Country', 'Latitude', 'Longitude', 'Temperature(C)', 'Humidity(%)', 'Cloudiness(%)',
                        'Wind Speed(MpH)', 'Date']
final_owm_df.to_csv('Output/Weather_owm.csv')
print(final_owm_df.count())
final_owm_df.head()
```

    Name               908
    Country            908
    Latitude           908
    Longitude          908
    Temperature(C)     908
    Humidity(%)        908
    Cloudiness(%)      908
    Wind Speed(MpH)    908
    Date               908
    dtype: int64





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Name</th>
      <th>Country</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Temperature(C)</th>
      <th>Humidity(%)</th>
      <th>Cloudiness(%)</th>
      <th>Wind Speed(MpH)</th>
      <th>Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Mar del Plata</td>
      <td>AR</td>
      <td>-46.43</td>
      <td>-67.52</td>
      <td>21.80</td>
      <td>48</td>
      <td>48</td>
      <td>11.01</td>
      <td>1522711326</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cape Town</td>
      <td>ZA</td>
      <td>-33.93</td>
      <td>18.42</td>
      <td>20.00</td>
      <td>68</td>
      <td>0</td>
      <td>7.20</td>
      <td>1522706400</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Rikitea</td>
      <td>PF</td>
      <td>-23.12</td>
      <td>-134.97</td>
      <td>25.75</td>
      <td>100</td>
      <td>32</td>
      <td>2.21</td>
      <td>1522711570</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Port Elizabeth</td>
      <td>US</td>
      <td>39.31</td>
      <td>-74.98</td>
      <td>6.00</td>
      <td>65</td>
      <td>90</td>
      <td>2.36</td>
      <td>1522709760</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Grand Gaube</td>
      <td>MU</td>
      <td>-20.01</td>
      <td>57.66</td>
      <td>26.00</td>
      <td>88</td>
      <td>75</td>
      <td>2.10</td>
      <td>1522710000</td>
    </tr>
  </tbody>
</table>
</div>



**Temperature vs Latitude Plot**


```python
# Check  our data, if the cities are spread enough
current_date = datetime.datetime.now().date().strftime("%m.%d.%Y")
plt.figure(figsize=(9,5))
plt.scatter(final_owm_df['Longitude'], final_owm_df['Latitude'], c=final_owm_df['Temperature(C)'], cmap='coolwarm')
plt.colorbar()
plt.grid(linestyle='dashed')
plt.title(f'World Temperature map on ({current_date})')
plt.savefig(f'Output/World_temperature_map_({current_date}).png')
```


![png](output_18_0.png)



```python
sns.set()
basedf = final_owm_df # DataFrame source for all plots can be easilly changed
plt.figure(figsize=(10,7))
plt.scatter(basedf['Temperature(C)'], basedf['Latitude'], c=basedf['Temperature(C)'], s=30, cmap='coolwarm')
plt.ylabel('Latitude')
plt.xlabel('Temperature (C)')
plt.title(f'Temperature (C) vs. City Latitude ({current_date})')
plt.tight_layout()
plt.savefig(f'Output/Temp_vs_City Latitude_({current_date}).png')
```


![png](output_19_0.png)


**Latitude vs Humidity, Cloudiness and Wind Speed Plots**


```python
style.use('bmh')
fig, (ax1, ax2, ax3) = plt.subplots(3, sharex=False)
#ax1.set_xlim(min(df_weather['Latitude'])-16, max(df_weather['Latitude'])+20)
fig.set_size_inches(13,25)

ax1.scatter(basedf['Latitude'], basedf['Humidity(%)'], c='g', s=20)
ax1.set_xlabel('City Latitude')
ax1.set_ylabel('Humidity (%)')
ax1.set_title(f'City Latitude vs City Humidity ({current_date})')

ax2.scatter(basedf['Latitude'], basedf['Cloudiness(%)'], c='b', s=20)
ax2.set_xlabel('City Latitude')
ax2.set_ylabel('Cloudiness (%)')
ax2.set_title(f'City Latitude vs City Cloudiness ({current_date})')

ax3.scatter(basedf['Latitude'], basedf['Wind Speed(MpH)'], c='r', s=20)
ax3.set_xlabel('City Latitude')
ax3.set_ylabel('Wind Speed (KMpH)')
ax3.set_title(f'City Latitude vs City Wind Speed ({current_date})')
plt.savefig(f'Output/Latitude_vs_Humid_Cloud_Wind_({current_date}).png')
```


![png](output_21_0.png)



```python
# Some more plots for fun
feature_list = ['Humidity(%)', 'Cloudiness(%)', 'Wind Speed(MpH)']
colors = cycle(['Greens', 'Blues', 'Oranges'])
plt.figure(figsize=(10,20))
for x in range(len(feature_list)):
    plt.subplot(3,1,x+1)
    plt.scatter(final_owm_df['Longitude'], final_owm_df['Latitude'], c=final_owm_df[feature_list[x]], cmap=next(colors))
    plt.title(f'{feature_list[x]} on the world map on ({current_date})')
    plt.colorbar()
    plt.ylabel('Latitude')
    plt.xlabel('Longitude')
plt.savefig(f'Output/World_maps_additions_({current_date}).png')
```


![png](output_22_0.png)

