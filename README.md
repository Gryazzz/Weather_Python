
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
```

**Create a list of random cities**

# this code kills the kernel, what I did wrong?
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

**This works fine, but the output number of cities is unpredictable**


```python
cities = {}

for x in range(2000):
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

    755


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

    Now processing: city number 1, name georgetown
    Now processing: city number 2, name honningsvag
    Now processing: city number 3, name kieta
    Now processing: city number 4, name lebu
    Now processing: city number 5, name provideniya
    Now processing: city number 6, name lavrentiya
    Now processing: city number 7, name olinda
    Now processing: city number 8, name rikitea
    Now processing: city number 9, name maldonado
    Now processing: city number 10, name pevek
    Now processing: city number 11, name hilo
    Now processing: city number 12, name acapulco
    Now processing: city number 13, name mataura
    Now processing: city number 14, name isangel
    Now processing: city number 15, name klaksvik
    Now processing: city number 16, name tasiilaq
    Now processing: city number 17, name belushya guba
    There is no city belushya guba in openweathermap database
    Now processing: city number 17, name kapaa
    Now processing: city number 18, name valparaiso
    Now processing: city number 19, name longyearbyen
    Now processing: city number 20, name albany
    Now processing: city number 21, name avarua
    Now processing: city number 22, name mar del plata
    Now processing: city number 23, name alekseyevsk
    Now processing: city number 24, name srisailam
    Now processing: city number 25, name santa cruz
    Now processing: city number 26, name ribeira brava
    Now processing: city number 27, name yellowknife
    Now processing: city number 28, name sentyabrskiy
    There is no city sentyabrskiy in openweathermap database
    Now processing: city number 28, name beboto
    Now processing: city number 29, name talnakh
    Now processing: city number 30, name chapais
    Now processing: city number 31, name kruisfontein
    Now processing: city number 32, name punta arenas
    Now processing: city number 33, name lorengau
    Now processing: city number 34, name jamestown
    Now processing: city number 35, name souillac
    Now processing: city number 36, name new norfolk
    Now processing: city number 37, name guerrero negro
    Now processing: city number 38, name southbridge
    Now processing: city number 39, name san patricio
    Now processing: city number 40, name severo-kurilsk
    Now processing: city number 41, name bethel
    Now processing: city number 42, name ylivieska
    Now processing: city number 43, name upernavik
    Now processing: city number 44, name hermanus
    Now processing: city number 45, name arraial do cabo
    Now processing: city number 46, name butaritari
    Now processing: city number 47, name deputatskiy
    Now processing: city number 48, name bluff
    Now processing: city number 49, name ascension
    There is no city ascension in openweathermap database
    Now processing: city number 49, name zhuhai
    Now processing: city number 50, name quatre cocos
    Now processing: city number 51, name portland
    Now processing: city number 52, name raglan
    Now processing: city number 53, name east london
    Now processing: city number 54, name oudtshoorn
    Now processing: city number 55, name barentsburg
    There is no city barentsburg in openweathermap database
    Now processing: city number 55, name taolanaro
    There is no city taolanaro in openweathermap database
    Now processing: city number 55, name saint george
    Now processing: city number 56, name hobart
    Now processing: city number 57, name bredasdorp
    Now processing: city number 58, name puerto ayora
    Now processing: city number 59, name goderich
    Now processing: city number 60, name vardo
    Now processing: city number 61, name ulaangom
    Now processing: city number 62, name dan khun thot
    Now processing: city number 63, name myitkyina
    Now processing: city number 64, name airai
    Now processing: city number 65, name vila franca do campo
    Now processing: city number 66, name presidencia roque saenz pena
    Now processing: city number 67, name dudinka
    Now processing: city number 68, name suez
    Now processing: city number 69, name mehamn
    Now processing: city number 70, name eurajoki
    Now processing: city number 71, name lincoln
    Now processing: city number 72, name semarang
    Now processing: city number 73, name faanui
    Now processing: city number 74, name san cristobal
    Now processing: city number 75, name busselton
    Now processing: city number 76, name mareeba
    Now processing: city number 77, name port alfred
    Now processing: city number 78, name amderma
    There is no city amderma in openweathermap database
    Now processing: city number 78, name illoqqortoormiut
    There is no city illoqqortoormiut in openweathermap database
    Now processing: city number 78, name katsuura
    Now processing: city number 79, name vaini
    Now processing: city number 80, name barrow
    Now processing: city number 81, name voh
    Now processing: city number 82, name ilulissat
    Now processing: city number 83, name dikson
    Now processing: city number 84, name safford
    Now processing: city number 85, name sakakah
    There is no city sakakah in openweathermap database
    Now processing: city number 85, name bayir
    Now processing: city number 86, name thompson
    Now processing: city number 87, name joshimath
    Now processing: city number 88, name saskylakh
    Now processing: city number 89, name sainte-suzanne
    Now processing: city number 90, name laguna
    Now processing: city number 91, name yar-sale
    Now processing: city number 92, name saint-ambroise
    Now processing: city number 93, name birmitrapur
    Now processing: city number 94, name marv dasht
    There is no city marv dasht in openweathermap database
    Now processing: city number 94, name gubkinskiy
    Now processing: city number 95, name dingli
    Now processing: city number 96, name richards bay
    Now processing: city number 97, name carnarvon
    Now processing: city number 98, name esperance
    Now processing: city number 99, name chilliwack
    Now processing: city number 100, name ushuaia
    Now processing: city number 101, name olafsvik
    There is no city olafsvik in openweathermap database
    Now processing: city number 101, name luderitz
    Now processing: city number 102, name one hundred mile house
    There is no city one hundred mile house in openweathermap database
    Now processing: city number 102, name ayan
    Now processing: city number 103, name batagay
    Now processing: city number 104, name qaanaaq
    Now processing: city number 105, name ballina
    Now processing: city number 106, name galle
    Now processing: city number 107, name smithers
    Now processing: city number 108, name carutapera
    Now processing: city number 109, name la ronge
    Now processing: city number 110, name mount isa
    Now processing: city number 111, name chuy
    Now processing: city number 112, name nyurba
    Now processing: city number 113, name sibolga
    Now processing: city number 114, name mys shmidta
    There is no city mys shmidta in openweathermap database
    Now processing: city number 114, name quartz hill
    Now processing: city number 115, name santa lucia
    Now processing: city number 116, name omboue
    Now processing: city number 117, name hamilton
    Now processing: city number 118, name touros
    Now processing: city number 119, name praia da vitoria
    Now processing: city number 120, name yeppoon
    Now processing: city number 121, name port elizabeth
    Now processing: city number 122, name castro
    Now processing: city number 123, name qaqortoq
    Now processing: city number 124, name sioux lookout
    Now processing: city number 125, name coatzintla
    Now processing: city number 126, name iranshahr
    Now processing: city number 127, name lhokseumawe
    Now processing: city number 128, name puerto del rosario
    Now processing: city number 129, name gazanjyk
    Now processing: city number 130, name rtishchevo
    Now processing: city number 131, name karkaralinsk
    There is no city karkaralinsk in openweathermap database
    Now processing: city number 131, name san jeronimo
    Now processing: city number 132, name myaundzha
    Now processing: city number 133, name bitung
    Now processing: city number 134, name port macquarie
    Now processing: city number 135, name atuona
    Now processing: city number 136, name shelburne
    Now processing: city number 137, name yatou
    Now processing: city number 138, name ponta do sol
    Now processing: city number 139, name nelson bay
    Now processing: city number 140, name poum
    Now processing: city number 141, name margate
    Now processing: city number 142, name high rock
    Now processing: city number 143, name cairns
    Now processing: city number 144, name colares
    Now processing: city number 145, name naryan-mar
    Now processing: city number 146, name iqaluit
    Now processing: city number 147, name kattivakkam
    Now processing: city number 148, name barra do bugres
    Now processing: city number 149, name natal
    Now processing: city number 150, name saint-philippe
    Now processing: city number 151, name lubumbashi
    Now processing: city number 152, name saint-joseph
    Now processing: city number 153, name yabelo
    Now processing: city number 154, name moron
    Now processing: city number 155, name amsterdam
    Now processing: city number 156, name ermelo
    Now processing: city number 157, name ust-ishim
    Now processing: city number 158, name puerto madryn
    Now processing: city number 159, name okha
    Now processing: city number 160, name coihueco
    Now processing: city number 161, name tuktoyaktuk
    Now processing: city number 162, name oussouye
    Now processing: city number 163, name lukovetskiy
    Now processing: city number 164, name henties bay
    Now processing: city number 165, name lososina
    Now processing: city number 166, name cherskiy
    Now processing: city number 167, name nhulunbuy
    Now processing: city number 168, name nioki
    Now processing: city number 169, name kabwe
    Now processing: city number 170, name mabaruma
    Now processing: city number 171, name lagdo
    Now processing: city number 172, name sao filipe
    Now processing: city number 173, name gunjur
    Now processing: city number 174, name palabuhanratu
    There is no city palabuhanratu in openweathermap database
    Now processing: city number 174, name inderborskiy
    There is no city inderborskiy in openweathermap database
    Now processing: city number 174, name calabozo
    Now processing: city number 175, name vanimo
    Now processing: city number 176, name brae
    Now processing: city number 177, name nikolskoye
    Now processing: city number 178, name sanislau
    Now processing: city number 179, name te anau
    Now processing: city number 180, name chunskiy
    Now processing: city number 181, name faya
    Now processing: city number 182, name fengrun
    Now processing: city number 183, name mayo
    Now processing: city number 184, name san ignacio
    Now processing: city number 185, name victoria
    Now processing: city number 186, name puerto narino
    Now processing: city number 187, name krasnoselkup
    There is no city krasnoselkup in openweathermap database
    Now processing: city number 187, name geraldton
    Now processing: city number 188, name northam
    Now processing: city number 189, name pangnirtung
    Now processing: city number 190, name matara
    Now processing: city number 191, name belmonte
    Now processing: city number 192, name kahului
    Now processing: city number 193, name beloha
    Now processing: city number 194, name leczna
    Now processing: city number 195, name chokurdakh
    Now processing: city number 196, name neuquen
    Now processing: city number 197, name bonfim
    Now processing: city number 198, name moranbah
    Now processing: city number 199, name port-cartier
    Now processing: city number 200, name ruatoria
    There is no city ruatoria in openweathermap database
    Now processing: city number 200, name mahebourg
    Now processing: city number 201, name khonsa
    Now processing: city number 202, name nosy varika
    Now processing: city number 203, name nanortalik
    Now processing: city number 204, name tumannyy
    There is no city tumannyy in openweathermap database
    Now processing: city number 204, name saint-pierre
    Now processing: city number 205, name yialos
    There is no city yialos in openweathermap database
    Now processing: city number 205, name kavaratti
    Now processing: city number 206, name bulgan
    Now processing: city number 207, name husavik
    Now processing: city number 208, name umm kaddadah
    Now processing: city number 209, name baykit
    Now processing: city number 210, name yambio
    There is no city yambio in openweathermap database
    Now processing: city number 210, name kirakira
    Now processing: city number 211, name los llanos de aridane
    Now processing: city number 212, name tadine
    Now processing: city number 213, name mahon
    Now processing: city number 214, name meyungs
    There is no city meyungs in openweathermap database
    Now processing: city number 214, name leningradskiy
    Now processing: city number 215, name plettenberg bay
    Now processing: city number 216, name agirish
    Now processing: city number 217, name khandbari
    Now processing: city number 218, name synya
    Now processing: city number 219, name norman wells
    Now processing: city number 220, name flinders
    Now processing: city number 221, name namibe
    Now processing: city number 222, name erzin
    Now processing: city number 223, name karratha
    Now processing: city number 224, name anchorage
    Now processing: city number 225, name san juan evangelista
    Now processing: city number 226, name kidal
    Now processing: city number 227, name torbay
    Now processing: city number 228, name cape town
    Now processing: city number 229, name port-gentil
    Now processing: city number 230, name bengkulu
    There is no city bengkulu in openweathermap database
    Now processing: city number 230, name saint-louis
    Now processing: city number 231, name kuytun
    Now processing: city number 232, name krasnyy chikoy
    Now processing: city number 233, name hearst
    Now processing: city number 234, name marmande
    Now processing: city number 235, name narsaq
    Now processing: city number 236, name tual
    Now processing: city number 237, name muros
    Now processing: city number 238, name dunedin
    Now processing: city number 239, name itupiranga
    Now processing: city number 240, name acajutla
    Now processing: city number 241, name santa maria
    Now processing: city number 242, name okhotsk
    Now processing: city number 243, name nemuro
    Now processing: city number 244, name saleaula
    There is no city saleaula in openweathermap database
    Now processing: city number 244, name mouzouras
    Now processing: city number 245, name broken hill
    Now processing: city number 246, name tsihombe
    There is no city tsihombe in openweathermap database
    Now processing: city number 246, name chikwawa
    Now processing: city number 247, name fuyang
    Now processing: city number 248, name calvia
    Now processing: city number 249, name srednekolymsk
    Now processing: city number 250, name bara
    Now processing: city number 251, name arraias
    Now processing: city number 252, name biak
    Now processing: city number 253, name sao joao da barra
    Now processing: city number 254, name hun
    Now processing: city number 255, name hithadhoo
    Now processing: city number 256, name srandakan
    Now processing: city number 257, name hawera
    Now processing: city number 258, name dingle
    Now processing: city number 259, name pedasi
    Now processing: city number 260, name grand river south east
    There is no city grand river south east in openweathermap database
    Now processing: city number 260, name moyale
    Now processing: city number 261, name warrington
    Now processing: city number 262, name maxixe
    Now processing: city number 263, name sicamous
    Now processing: city number 264, name huancavelica
    Now processing: city number 265, name adamas
    Now processing: city number 266, name umzimvubu
    There is no city umzimvubu in openweathermap database
    Now processing: city number 266, name temaraia
    There is no city temaraia in openweathermap database
    Now processing: city number 266, name port blair
    Now processing: city number 267, name colac
    Now processing: city number 268, name la roche-sur-yon
    Now processing: city number 269, name noumea
    Now processing: city number 270, name tshikapa
    Now processing: city number 271, name waddan
    Now processing: city number 272, name saint-michel-des-saints
    Now processing: city number 273, name mandalgovi
    Now processing: city number 274, name misratah
    Now processing: city number 275, name adelaide
    Now processing: city number 276, name miracema do tocantins
    Now processing: city number 277, name buqayq
    There is no city buqayq in openweathermap database
    Now processing: city number 277, name bismarck
    Now processing: city number 278, name bubaque
    Now processing: city number 279, name isabela
    Now processing: city number 280, name clyde river
    Now processing: city number 281, name ndago
    Now processing: city number 282, name tiznit
    Now processing: city number 283, name solnechnyy
    Now processing: city number 284, name la baule-escoublac
    Now processing: city number 285, name udachnyy
    Now processing: city number 286, name kalabo
    Now processing: city number 287, name naftah
    There is no city naftah in openweathermap database
    Now processing: city number 287, name dubuque
    Now processing: city number 288, name aripuana
    Now processing: city number 289, name cidreira
    Now processing: city number 290, name ahipara
    Now processing: city number 291, name bajo baudo
    There is no city bajo baudo in openweathermap database
    Now processing: city number 291, name axim
    Now processing: city number 292, name kodiak
    Now processing: city number 293, name ribeira grande
    Now processing: city number 294, name nakamura
    Now processing: city number 295, name necochea
    Now processing: city number 296, name play cu
    There is no city play cu in openweathermap database
    Now processing: city number 296, name payson
    Now processing: city number 297, name salinopolis
    Now processing: city number 298, name waterloo
    Now processing: city number 299, name sorong
    Now processing: city number 300, name taitung
    Now processing: city number 301, name maumere
    Now processing: city number 302, name saint-georges
    Now processing: city number 303, name lagoa
    Now processing: city number 304, name kaitangata
    Now processing: city number 305, name korla
    There is no city korla in openweathermap database
    Now processing: city number 305, name lasa
    Now processing: city number 306, name kavieng
    Now processing: city number 307, name salalah
    Now processing: city number 308, name khatanga
    Now processing: city number 309, name menongue
    Now processing: city number 310, name abiramam
    Now processing: city number 311, name marcona
    There is no city marcona in openweathermap database
    Now processing: city number 311, name lompoc
    Now processing: city number 312, name nizhneyansk
    There is no city nizhneyansk in openweathermap database
    Now processing: city number 312, name miri
    Now processing: city number 313, name uwayl
    There is no city uwayl in openweathermap database
    Now processing: city number 313, name poitiers
    Now processing: city number 314, name terrace
    Now processing: city number 315, name port augusta
    Now processing: city number 316, name tilichiki
    Now processing: city number 317, name general artigas
    Now processing: city number 318, name buala
    Now processing: city number 319, name hihifo
    There is no city hihifo in openweathermap database
    Now processing: city number 319, name namatanai
    Now processing: city number 320, name nanakuli
    Now processing: city number 321, name moa
    Now processing: city number 322, name nipawin
    Now processing: city number 323, name north myrtle beach
    Now processing: city number 324, name miguel hidalgo
    Now processing: city number 325, name semey
    Now processing: city number 326, name huntington
    Now processing: city number 327, name port hedland
    Now processing: city number 328, name ilovlya
    Now processing: city number 329, name alofi
    Now processing: city number 330, name setermoen
    Now processing: city number 331, name babol
    Now processing: city number 332, name ndele
    There is no city ndele in openweathermap database
    Now processing: city number 332, name codrington
    Now processing: city number 333, name nouadhibou
    Now processing: city number 334, name emerald
    Now processing: city number 335, name bestobe
    Now processing: city number 336, name darhan
    Now processing: city number 337, name saldanha
    Now processing: city number 338, name roald
    Now processing: city number 339, name tura
    Now processing: city number 340, name halalo
    There is no city halalo in openweathermap database
    Now processing: city number 340, name katangli
    Now processing: city number 341, name sabzevar
    Now processing: city number 342, name terme
    Now processing: city number 343, name kimbe
    Now processing: city number 344, name pineville
    Now processing: city number 345, name cabo san lucas
    Now processing: city number 346, name payakumbuh
    Now processing: city number 347, name bilma
    Now processing: city number 348, name mackay
    Now processing: city number 349, name jahangirabad
    Now processing: city number 350, name neftegorsk
    Now processing: city number 351, name batemans bay
    Now processing: city number 352, name sorvag
    There is no city sorvag in openweathermap database
    Now processing: city number 352, name hambantota
    Now processing: city number 353, name san jose
    Now processing: city number 354, name khormuj
    There is no city khormuj in openweathermap database
    Now processing: city number 354, name barabai
    Now processing: city number 355, name talaya
    Now processing: city number 356, name tawkar
    There is no city tawkar in openweathermap database
    Now processing: city number 356, name kamenskoye
    There is no city kamenskoye in openweathermap database
    Now processing: city number 356, name san matias
    Now processing: city number 357, name olga
    Now processing: city number 358, name horta
    Now processing: city number 359, name vila velha
    Now processing: city number 360, name grindavik
    Now processing: city number 361, name bayji
    Now processing: city number 362, name mrirt
    There is no city mrirt in openweathermap database
    Now processing: city number 362, name peniche
    Now processing: city number 363, name mocuba
    Now processing: city number 364, name ternate
    Now processing: city number 365, name lolua
    There is no city lolua in openweathermap database
    Now processing: city number 365, name kaliua
    Now processing: city number 366, name cap malheureux
    Now processing: city number 367, name abdulino
    Now processing: city number 368, name montague
    Now processing: city number 369, name haapu
    There is no city haapu in openweathermap database
    Now processing: city number 369, name shiloh
    Now processing: city number 370, name zhicheng
    Now processing: city number 371, name impfondo
    Now processing: city number 372, name ojinaga
    Now processing: city number 373, name rabo de peixe
    Now processing: city number 374, name fairbanks
    Now processing: city number 375, name pacific grove
    Now processing: city number 376, name sagua la grande
    Now processing: city number 377, name chernyshevskiy
    Now processing: city number 378, name itarema
    Now processing: city number 379, name mogocha
    Now processing: city number 380, name assiniboia
    Now processing: city number 381, name broome
    Now processing: city number 382, name mayor pablo lagerenza
    Now processing: city number 383, name maragogi
    Now processing: city number 384, name gasa
    Now processing: city number 385, name coihaique
    Now processing: city number 386, name port lincoln
    Now processing: city number 387, name gat
    Now processing: city number 388, name boende
    Now processing: city number 389, name lima
    Now processing: city number 390, name pisco
    Now processing: city number 391, name afmadu
    There is no city afmadu in openweathermap database
    Now processing: city number 391, name sucua
    Now processing: city number 392, name vostok
    Now processing: city number 393, name porto santo
    There is no city porto santo in openweathermap database
    Now processing: city number 393, name ust-tarka
    Now processing: city number 394, name anopino
    Now processing: city number 395, name attawapiskat
    There is no city attawapiskat in openweathermap database
    Now processing: city number 395, name svetlaya
    Now processing: city number 396, name salto
    Now processing: city number 397, name inirida
    Now processing: city number 398, name coruripe
    Now processing: city number 399, name jimenez
    Now processing: city number 400, name vicuna
    There is no city vicuna in openweathermap database
    Now processing: city number 400, name gualeguaychu
    Now processing: city number 401, name kedrovyy
    Now processing: city number 402, name tiksi
    Now processing: city number 403, name vysokogornyy
    Now processing: city number 404, name seoul
    Now processing: city number 405, name tyrma
    Now processing: city number 406, name vao
    Now processing: city number 407, name nago
    Now processing: city number 408, name shingu
    Now processing: city number 409, name mumford
    Now processing: city number 410, name akureyri
    Now processing: city number 411, name lata
    Now processing: city number 412, name tublay
    Now processing: city number 413, name belaya gora
    Now processing: city number 414, name prainha
    Now processing: city number 415, name hovd
    Now processing: city number 416, name aklavik
    Now processing: city number 417, name gizo
    Now processing: city number 418, name kuala krai
    There is no city kuala krai in openweathermap database
    Now processing: city number 418, name songea
    Now processing: city number 419, name copiapo
    Now processing: city number 420, name itarantim
    Now processing: city number 421, name zeya
    Now processing: city number 422, name hanyang
    Now processing: city number 423, name awjilah
    Now processing: city number 424, name dekar
    Now processing: city number 425, name vangaindrano
    Now processing: city number 426, name naze
    Now processing: city number 427, name saint-augustin
    Now processing: city number 428, name kindu
    Now processing: city number 429, name nan
    Now processing: city number 430, name kitimat
    Now processing: city number 431, name flin flon
    Now processing: city number 432, name owosso
    Now processing: city number 433, name skopelos
    Now processing: city number 434, name louisbourg
    There is no city louisbourg in openweathermap database
    Now processing: city number 434, name araouane
    Now processing: city number 435, name old saybrook
    Now processing: city number 436, name sobolevo
    Now processing: city number 437, name komsomolskiy
    Now processing: city number 438, name mount gambier
    Now processing: city number 439, name rocha
    Now processing: city number 440, name meulaboh
    Now processing: city number 441, name zaysan
    Now processing: city number 442, name bilibino
    Now processing: city number 443, name nara
    Now processing: city number 444, name outjo
    Now processing: city number 445, name ewa beach
    Now processing: city number 446, name san francisco
    Now processing: city number 447, name nioro
    Now processing: city number 448, name hasaki
    Now processing: city number 449, name nuuk
    Now processing: city number 450, name campoverde
    Now processing: city number 451, name lakatoro
    Now processing: city number 452, name labuhan
    Now processing: city number 453, name salgado
    Now processing: city number 454, name jiddah
    There is no city jiddah in openweathermap database
    Now processing: city number 454, name cortez
    Now processing: city number 455, name san rafael
    Now processing: city number 456, name puebloviejo
    Now processing: city number 457, name ayacucho
    Now processing: city number 458, name tessalit
    Now processing: city number 459, name imbituba
    Now processing: city number 460, name ambulu
    Now processing: city number 461, name bay roberts
    Now processing: city number 462, name jardim
    Now processing: city number 463, name teapa
    Now processing: city number 464, name buncrana
    Now processing: city number 465, name hvide sande
    Now processing: city number 466, name teguise
    Now processing: city number 467, name skagastrond
    There is no city skagastrond in openweathermap database
    Now processing: city number 467, name lang suan
    Now processing: city number 468, name toliary
    There is no city toliary in openweathermap database
    Now processing: city number 468, name oistins
    Now processing: city number 469, name bolungarvik
    There is no city bolungarvik in openweathermap database
    Now processing: city number 469, name waw
    There is no city waw in openweathermap database
    Now processing: city number 469, name achacachi
    Now processing: city number 470, name cayenne
    Now processing: city number 471, name parral
    Now processing: city number 472, name miyako
    Now processing: city number 473, name polikhnitos
    There is no city polikhnitos in openweathermap database
    Now processing: city number 473, name taywarah
    Now processing: city number 474, name saryg-sep
    Now processing: city number 475, name plouzane
    Now processing: city number 476, name buta
    Now processing: city number 477, name hellin
    Now processing: city number 478, name marabba
    Now processing: city number 479, name puri
    Now processing: city number 480, name ijaki
    There is no city ijaki in openweathermap database
    Now processing: city number 480, name antibes
    Now processing: city number 481, name sitka
    Now processing: city number 482, name mahalapye
    Now processing: city number 483, name ndungu
    Now processing: city number 484, name escuinapa
    There is no city escuinapa in openweathermap database
    Now processing: city number 484, name kununurra
    Now processing: city number 485, name esso
    Now processing: city number 486, name yarim
    Now processing: city number 487, name constitucion
    Now processing: city number 488, name lagunas
    Now processing: city number 489, name najran
    Now processing: city number 490, name avera
    Now processing: city number 491, name tawang
    Now processing: city number 492, name kloulklubed
    Now processing: city number 493, name tongren
    Now processing: city number 494, name lokosovo
    Now processing: city number 495, name egvekinot
    Now processing: city number 496, name caravelas
    Now processing: city number 497, name rhyl
    Now processing: city number 498, name chippewa falls
    Now processing: city number 499, name saint anthony
    Now processing: city number 500, name luba
    Now processing: city number 501, name mezhdurechensk
    Now processing: city number 502, name evensk
    Now processing: city number 503, name rio gallegos
    Now processing: city number 504, name mega
    Now processing: city number 505, name mill valley
    Now processing: city number 506, name elektrenai
    Now processing: city number 507, name balykshi
    There is no city balykshi in openweathermap database
    Now processing: city number 507, name robertson
    Now processing: city number 508, name shaartuz
    There is no city shaartuz in openweathermap database
    Now processing: city number 508, name grand gaube
    Now processing: city number 509, name great yarmouth
    Now processing: city number 510, name san quintin
    Now processing: city number 511, name valente
    Now processing: city number 512, name camopi
    Now processing: city number 513, name sao geraldo do araguaia
    Now processing: city number 514, name jabinyanah
    Now processing: city number 515, name half moon bay
    Now processing: city number 516, name chulman
    Now processing: city number 517, name baruun-urt
    Now processing: city number 518, name nichinan
    Now processing: city number 519, name waipawa
    Now processing: city number 520, name ibaiti
    Now processing: city number 521, name tehri
    Now processing: city number 522, name mollendo
    Now processing: city number 523, name zopilotepe
    Now processing: city number 524, name samusu
    There is no city samusu in openweathermap database
    Now processing: city number 524, name labutta
    There is no city labutta in openweathermap database
    Now processing: city number 524, name ahualulco
    Now processing: city number 525, name zhigansk
    Now processing: city number 526, name pietermaritzburg
    Now processing: city number 527, name irati
    Now processing: city number 528, name ilebo
    Now processing: city number 529, name rio grande
    Now processing: city number 530, name bastrop
    Now processing: city number 531, name swellendam
    Now processing: city number 532, name kontagora
    Now processing: city number 533, name beringovskiy
    Now processing: city number 534, name arlit
    Now processing: city number 535, name phulabani
    Now processing: city number 536, name montpellier
    Now processing: city number 537, name taboga
    Now processing: city number 538, name tecalitlan
    Now processing: city number 539, name rakai
    Now processing: city number 540, name kamaishi
    Now processing: city number 541, name tansen
    Now processing: city number 542, name dzhebariki-khaya
    Now processing: city number 543, name wobulenzi
    Now processing: city number 544, name sataua
    There is no city sataua in openweathermap database
    Now processing: city number 544, name el dorado
    Now processing: city number 545, name saryshagan
    There is no city saryshagan in openweathermap database
    Now processing: city number 545, name aflu
    There is no city aflu in openweathermap database
    Now processing: city number 545, name mardin
    Now processing: city number 546, name sovetskiy
    Now processing: city number 547, name tidore
    There is no city tidore in openweathermap database
    Now processing: city number 547, name tabiauea
    There is no city tabiauea in openweathermap database
    Now processing: city number 547, name gannan
    Now processing: city number 548, name medea
    Now processing: city number 549, name fortuna
    Now processing: city number 550, name san vicente
    Now processing: city number 551, name baniachang
    Now processing: city number 552, name puerto leguizamo
    Now processing: city number 553, name samalaeulu
    There is no city samalaeulu in openweathermap database
    Now processing: city number 553, name rumonge
    Now processing: city number 554, name makakilo city
    Now processing: city number 555, name houma
    Now processing: city number 556, name himora
    There is no city himora in openweathermap database
    Now processing: city number 556, name syracuse
    Now processing: city number 557, name harrisburg
    Now processing: city number 558, name ust-omchug
    Now processing: city number 559, name kazalinsk
    There is no city kazalinsk in openweathermap database
    Now processing: city number 559, name kaabong
    Now processing: city number 560, name padang
    Now processing: city number 561, name zhezkazgan
    Now processing: city number 562, name burns lake
    Now processing: city number 563, name asau
    There is no city asau in openweathermap database
    Now processing: city number 563, name north bend
    Now processing: city number 564, name kintampo
    Now processing: city number 565, name coos bay
    Now processing: city number 566, name douentza
    Now processing: city number 567, name barawe
    There is no city barawe in openweathermap database
    Now processing: city number 567, name kuhestan
    There is no city kuhestan in openweathermap database
    Now processing: city number 567, name skagen
    Now processing: city number 568, name khani
    Now processing: city number 569, name aleksandrov gay
    Now processing: city number 570, name alihe
    Now processing: city number 571, name antsohihy
    Now processing: city number 572, name bandrele
    Now processing: city number 573, name prince rupert
    Now processing: city number 574, name chute-aux-outardes
    Now processing: city number 575, name houlton
    Now processing: city number 576, name casambalangan
    Now processing: city number 577, name worland
    Now processing: city number 578, name la chaux-de-fonds
    Now processing: city number 579, name port arthur
    Now processing: city number 580, name the pas
    Now processing: city number 581, name panji
    Now processing: city number 582, name slave lake
    Now processing: city number 583, name mangrol
    Now processing: city number 584, name chililabombwe
    Now processing: city number 585, name palmer
    Now processing: city number 586, name nador
    Now processing: city number 587, name les cayes
    Now processing: city number 588, name gurgan
    There is no city gurgan in openweathermap database
    Now processing: city number 588, name bargal
    There is no city bargal in openweathermap database
    Now processing: city number 588, name gigmoto
    Now processing: city number 589, name sebastian
    Now processing: city number 590, name mancio lima
    There is no city mancio lima in openweathermap database
    Now processing: city number 590, name sept-iles
    Now processing: city number 591, name vestmannaeyjar
    Now processing: city number 592, name herran
    Now processing: city number 593, name pathein
    Now processing: city number 594, name ancud
    Now processing: city number 595, name anadyr
    Now processing: city number 596, name tecoanapa
    Now processing: city number 597, name hue
    Now processing: city number 598, name jundubah
    There is no city jundubah in openweathermap database
    Now processing: city number 598, name aksu
    Now processing: city number 599, name wahran
    There is no city wahran in openweathermap database
    Now processing: city number 599, name morgan city
    Now processing: city number 600, name unterseen
    Now processing: city number 601, name ladushkin
    Now processing: city number 602, name mazatlan
    Now processing: city number 603, name sisimiut
    Now processing: city number 604, name vanavara
    Now processing: city number 605, name galgani
    There is no city galgani in openweathermap database
    Now processing: city number 605, name la rioja
    Now processing: city number 606, name west wendover
    Now processing: city number 607, name bell ville
    Now processing: city number 608, name tateyama
    Now processing: city number 609, name west bay
    Now processing: city number 610, name vallenar
    Now processing: city number 611, name durban
    Now processing: city number 612, name haines junction
    Now processing: city number 613, name pout
    There is no city pout in openweathermap database
    Now processing: city number 613, name bud
    Now processing: city number 614, name marienburg
    Now processing: city number 615, name muskogee
    Now processing: city number 616, name camacha
    Now processing: city number 617, name homer
    Now processing: city number 618, name nicosia
    There is no city nicosia in openweathermap database
    Now processing: city number 618, name okitipupa
    Now processing: city number 619, name viedma
    Now processing: city number 620, name honiara
    Now processing: city number 621, name yantzaza
    Now processing: city number 622, name sheridan
    Now processing: city number 623, name basco
    Now processing: city number 624, name krian
    Now processing: city number 625, name pailitas
    Now processing: city number 626, name quelimane
    Now processing: city number 627, name puerto colombia
    Now processing: city number 628, name tunduru
    There is no city tunduru in openweathermap database
    Now processing: city number 628, name nykoping
    Now processing: city number 629, name port hardy
    Now processing: city number 630, name peremyshlyany
    Now processing: city number 631, name gemena
    Now processing: city number 632, name sola
    Now processing: city number 633, name lesnoy
    Now processing: city number 634, name tarauaca
    Now processing: city number 635, name mikun
    Now processing: city number 636, name haines city
    Now processing: city number 637, name liku
    Now processing: city number 638, name goba
    Now processing: city number 639, name luwuk
    Now processing: city number 640, name vaitupu
    There is no city vaitupu in openweathermap database
    Now processing: city number 640, name ulmu
    Now processing: city number 641, name bambous virieux
    Now processing: city number 642, name porto-vecchio
    Now processing: city number 643, name alice springs
    Now processing: city number 644, name ihosy
    Now processing: city number 645, name maibong
    Now processing: city number 646, name poya
    Now processing: city number 647, name xining
    Now processing: city number 648, name fasa
    Now processing: city number 649, name whitehorse
    Now processing: city number 650, name bay-khaak
    Now processing: city number 651, name havoysund
    Now processing: city number 652, name denpasar
    Now processing: city number 653, name jega
    Now processing: city number 654, name trairi
    Now processing: city number 655, name san lorenzo
    Now processing: city number 656, name rolla
    Now processing: city number 657, name chihuahua
    Now processing: city number 658, name harrison
    Now processing: city number 659, name brownfield
    Now processing: city number 660, name cedar city
    Now processing: city number 661, name hegang
    Now processing: city number 662, name liverpool
    Now processing: city number 663, name khonuu
    There is no city khonuu in openweathermap database
    Now processing: city number 663, name adre
    Now processing: city number 664, name san carlos
    Now processing: city number 665, name walvis bay
    Now processing: city number 666, name bambanglipuro
    Now processing: city number 667, name hofn
    Now processing: city number 668, name chopovychi
    Now processing: city number 669, name tautira
    Now processing: city number 670, name carupano
    85 cities were skipped
    670 cities data was captured


**Create a DataFrame based on Requests data**


```python
dframe_json = pd.DataFrame(all_requests_data)
final_req_df = dframe_json.transpose()
final_req_df.columns = ['Country', 'Latitude', 'Temperature(F)', 'Humidity(%)', 'Cloudiness(%)', 'Wind Speed(MpH)',
                        'Date']
final_req_df.to_csv('Output/Weather_requests.csv')
print(final_req_df.count())
final_req_df.head()
```

    Country            670
    Latitude           670
    Temperature(F)     670
    Humidity(%)        670
    Cloudiness(%)      670
    Wind Speed(MpH)    670
    Date               670
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
      <th>Temperature(F)</th>
      <th>Humidity(%)</th>
      <th>Cloudiness(%)</th>
      <th>Wind Speed(MpH)</th>
      <th>Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Abdulino</th>
      <td>RU</td>
      <td>53.68</td>
      <td>32.83</td>
      <td>100</td>
      <td>88</td>
      <td>12.1</td>
      <td>1522262461</td>
    </tr>
    <tr>
      <th>Abiramam</th>
      <td>IN</td>
      <td>9.44</td>
      <td>84.2</td>
      <td>70</td>
      <td>20</td>
      <td>7.96</td>
      <td>1522260000</td>
    </tr>
    <tr>
      <th>Acajutla</th>
      <td>SV</td>
      <td>13.59</td>
      <td>91.4</td>
      <td>63</td>
      <td>20</td>
      <td>5.82</td>
      <td>1522259400</td>
    </tr>
    <tr>
      <th>Acapulco</th>
      <td>MX</td>
      <td>16.86</td>
      <td>84.2</td>
      <td>66</td>
      <td>5</td>
      <td>5.82</td>
      <td>1522258800</td>
    </tr>
    <tr>
      <th>Achacachi</th>
      <td>BO</td>
      <td>-16.04</td>
      <td>52.63</td>
      <td>67</td>
      <td>48</td>
      <td>4.38</td>
      <td>1522262509</td>
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

    There is no city belushya guba in openweathermap database
    There is no city sentyabrskiy in openweathermap database
    There is no city ascension in openweathermap database
    There is no city barentsburg in openweathermap database
    There is no city taolanaro in openweathermap database
    There is no city amderma in openweathermap database
    There is no city illoqqortoormiut in openweathermap database
    There is no city sakakah in openweathermap database
    There is no city marv dasht in openweathermap database
    There is no city olafsvik in openweathermap database
    There is no city one hundred mile house in openweathermap database
    There is no city mys shmidta in openweathermap database
    There is no city karkaralinsk in openweathermap database
    There is no city palabuhanratu in openweathermap database
    There is no city inderborskiy in openweathermap database
    There is no city krasnoselkup in openweathermap database
    There is no city ruatoria in openweathermap database
    There is no city tumannyy in openweathermap database
    There is no city yialos in openweathermap database
    There is no city yambio in openweathermap database
    There is no city meyungs in openweathermap database
    There is no city bengkulu in openweathermap database
    There is no city saleaula in openweathermap database
    There is no city tsihombe in openweathermap database
    There is no city grand river south east in openweathermap database
    There is no city umzimvubu in openweathermap database
    There is no city temaraia in openweathermap database
    There is no city buqayq in openweathermap database
    There is no city naftah in openweathermap database
    There is no city bajo baudo in openweathermap database
    There is no city play cu in openweathermap database
    There is no city korla in openweathermap database
    There is no city marcona in openweathermap database
    There is no city nizhneyansk in openweathermap database
    There is no city uwayl in openweathermap database
    There is no city hihifo in openweathermap database
    There is no city ndele in openweathermap database
    There is no city halalo in openweathermap database
    There is no city sorvag in openweathermap database
    There is no city khormuj in openweathermap database
    There is no city tawkar in openweathermap database
    There is no city kamenskoye in openweathermap database
    There is no city mrirt in openweathermap database
    There is no city lolua in openweathermap database
    There is no city haapu in openweathermap database
    There is no city afmadu in openweathermap database
    There is no city porto santo in openweathermap database
    There is no city attawapiskat in openweathermap database
    There is no city vicuna in openweathermap database
    There is no city kuala krai in openweathermap database
    There is no city louisbourg in openweathermap database
    There is no city jiddah in openweathermap database
    There is no city skagastrond in openweathermap database
    There is no city toliary in openweathermap database
    There is no city bolungarvik in openweathermap database
    There is no city waw in openweathermap database
    There is no city polikhnitos in openweathermap database
    There is no city ijaki in openweathermap database
    There is no city escuinapa in openweathermap database
    There is no city balykshi in openweathermap database
    There is no city shaartuz in openweathermap database
    There is no city samusu in openweathermap database
    There is no city labutta in openweathermap database
    There is no city sataua in openweathermap database
    There is no city saryshagan in openweathermap database
    There is no city aflu in openweathermap database
    There is no city tidore in openweathermap database
    There is no city tabiauea in openweathermap database
    There is no city samalaeulu in openweathermap database
    There is no city himora in openweathermap database
    There is no city kazalinsk in openweathermap database
    There is no city asau in openweathermap database
    There is no city barawe in openweathermap database
    There is no city kuhestan in openweathermap database
    There is no city gurgan in openweathermap database
    There is no city bargal in openweathermap database
    There is no city mancio lima in openweathermap database
    There is no city jundubah in openweathermap database
    There is no city wahran in openweathermap database
    There is no city galgani in openweathermap database
    There is no city pout in openweathermap database
    There is no city nicosia in openweathermap database
    There is no city tunduru in openweathermap database
    There is no city vaitupu in openweathermap database
    There is no city khonuu in openweathermap database
    85 cities were skipped
    670 cities data was captured


**Create a DataFrame based on OWM data**


```python
search = ['name', 'sys.country', 'coord.lat', 'main.temp','main.humidity', 'clouds.all', 'wind.speed', 'dt']
extracted_data = [city(*search) for city in all_owm_data]

final_owm_df = pd.DataFrame(extracted_data)
final_owm_df.columns = ['Name','Country', 'Latitude', 'Temperature(C)', 'Humidity(%)', 'Cloudiness(%)',
                        'Wind Speed(MpH)', 'Date']
final_owm_df.to_csv('Output/Weather_owm.csv')
print(final_owm_df.count())
final_owm_df.head()
```

    Name               670
    Country            670
    Latitude           670
    Temperature(C)     670
    Humidity(%)        670
    Cloudiness(%)      670
    Wind Speed(MpH)    670
    Date               670
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
      <td>Georgetown</td>
      <td>GY</td>
      <td>6.80</td>
      <td>31.00</td>
      <td>58</td>
      <td>75</td>
      <td>6.20</td>
      <td>1522260000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Honningsvag</td>
      <td>NO</td>
      <td>70.98</td>
      <td>0.00</td>
      <td>100</td>
      <td>75</td>
      <td>5.10</td>
      <td>1522263000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Kieta</td>
      <td>PG</td>
      <td>-6.22</td>
      <td>26.24</td>
      <td>100</td>
      <td>20</td>
      <td>1.71</td>
      <td>1522263823</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Lebu</td>
      <td>ET</td>
      <td>8.96</td>
      <td>16.71</td>
      <td>44</td>
      <td>8</td>
      <td>3.86</td>
      <td>1522263823</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Provideniya</td>
      <td>RU</td>
      <td>64.42</td>
      <td>-6.84</td>
      <td>100</td>
      <td>76</td>
      <td>5.46</td>
      <td>1522263823</td>
    </tr>
  </tbody>
</table>
</div>



**Temperature vs Latitude Plot**


```python
sns.set()
basedf = final_owm_df # DataFrame source for all plots can be easilly changed
current_date = datetime.datetime.now().date().strftime("%m.%d.%Y")
plt.figure(figsize=(10,7))
plt.scatter(basedf['Temperature(C)'], basedf['Latitude'], c=basedf['Temperature(C)'], s=30, cmap='coolwarm')
plt.ylabel('Latitude')
plt.xlabel('Temperature (C)')
plt.title(f'Temperature (C) vs. City Latitude ({current_date})')
plt.tight_layout()
plt.savefig(f'Output/Temp_vs_City Latitude_({current_date}).png')
```


![png](output_18_0.png)


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


![png](output_20_0.png)

