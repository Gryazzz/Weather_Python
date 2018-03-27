
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
    cities[city] = coord

print(len(cities))
```

    772


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
        pass
    counter += 1
    
print(f'{absent_cities_req} cities were skipped')
print(f'{len(all_requests_data)} cities data was captured')
```

    Now processing: city number 1, name mys shmidta
    There is no city mys shmidta in openweathermap database
    Now processing: city number 2, name butterworth
    Now processing: city number 3, name furano
    Now processing: city number 4, name provideniya
    Now processing: city number 5, name ponta do sol
    Now processing: city number 6, name sint-truiden
    Now processing: city number 7, name ushuaia
    Now processing: city number 8, name mataura
    Now processing: city number 9, name new norfolk
    Now processing: city number 10, name kapaa
    Now processing: city number 11, name haines junction
    Now processing: city number 12, name hirara
    Now processing: city number 13, name manoel urbano
    Now processing: city number 14, name hermanus
    Now processing: city number 15, name avarua
    Now processing: city number 16, name taolanaro
    There is no city taolanaro in openweathermap database
    Now processing: city number 17, name vulcanesti
    Now processing: city number 18, name freeport
    Now processing: city number 19, name verkhoyansk
    Now processing: city number 20, name carnarvon
    Now processing: city number 21, name vaini
    Now processing: city number 22, name edd
    Now processing: city number 23, name albany
    Now processing: city number 24, name nome
    Now processing: city number 25, name saint-philippe
    Now processing: city number 26, name margate
    Now processing: city number 27, name san policarpo
    Now processing: city number 28, name yumen
    Now processing: city number 29, name luderitz
    Now processing: city number 30, name chekhov
    Now processing: city number 31, name nueva loja
    Now processing: city number 32, name torbay
    Now processing: city number 33, name isangel
    Now processing: city number 34, name jamestown
    Now processing: city number 35, name mecca
    Now processing: city number 36, name siavonga
    Now processing: city number 37, name mukhtolovo
    Now processing: city number 38, name chuy
    Now processing: city number 39, name akyab
    There is no city akyab in openweathermap database
    Now processing: city number 40, name thinadhoo
    Now processing: city number 41, name muriwai beach
    Now processing: city number 42, name barentsburg
    There is no city barentsburg in openweathermap database
    Now processing: city number 43, name rikitea
    Now processing: city number 44, name hobart
    Now processing: city number 45, name gat
    Now processing: city number 46, name lavumisa
    Now processing: city number 47, name punta arenas
    Now processing: city number 48, name esperance
    Now processing: city number 49, name ulagan
    Now processing: city number 50, name saint-augustin
    Now processing: city number 51, name solano
    Now processing: city number 52, name jumla
    Now processing: city number 53, name ahipara
    Now processing: city number 54, name clyde river
    Now processing: city number 55, name ixtapa
    Now processing: city number 56, name kautokeino
    Now processing: city number 57, name puerto ayora
    Now processing: city number 58, name abalak
    Now processing: city number 59, name san patricio
    Now processing: city number 60, name ostrovnoy
    Now processing: city number 61, name illoqqortoormiut
    There is no city illoqqortoormiut in openweathermap database
    Now processing: city number 62, name chapais
    Now processing: city number 63, name talnakh
    Now processing: city number 64, name castro
    Now processing: city number 65, name vigevano
    Now processing: city number 66, name vanimo
    Now processing: city number 67, name tongliao
    Now processing: city number 68, name makakilo city
    Now processing: city number 69, name porto novo
    Now processing: city number 70, name mahebourg
    Now processing: city number 71, name steinbach
    Now processing: city number 72, name hammerfest
    Now processing: city number 73, name sentyabrskiy
    There is no city sentyabrskiy in openweathermap database
    Now processing: city number 74, name port alfred
    Now processing: city number 75, name tasiilaq
    Now processing: city number 76, name georgetown
    Now processing: city number 77, name kolobrzeg
    Now processing: city number 78, name mar del plata
    Now processing: city number 79, name the valley
    Now processing: city number 80, name bethel
    Now processing: city number 81, name tuktoyaktuk
    Now processing: city number 82, name sri aman
    Now processing: city number 83, name emerald
    Now processing: city number 84, name fukue
    Now processing: city number 85, name pevek
    Now processing: city number 86, name skagastrond
    There is no city skagastrond in openweathermap database
    Now processing: city number 87, name angoram
    Now processing: city number 88, name east london
    Now processing: city number 89, name pacific grove
    Now processing: city number 90, name starobesheve
    Now processing: city number 91, name lincoln
    Now processing: city number 92, name somerset east
    Now processing: city number 93, name brandon
    Now processing: city number 94, name thompson
    Now processing: city number 95, name berbera
    There is no city berbera in openweathermap database
    Now processing: city number 96, name hamilton
    Now processing: city number 97, name cape town
    Now processing: city number 98, name pangnirtung
    Now processing: city number 99, name maputo
    Now processing: city number 100, name kastamonu
    Now processing: city number 101, name palabuhanratu
    There is no city palabuhanratu in openweathermap database
    Now processing: city number 102, name chokurdakh
    Now processing: city number 103, name nikolskoye
    Now processing: city number 104, name puerto quijarro
    Now processing: city number 105, name lata
    Now processing: city number 106, name egvekinot
    Now processing: city number 107, name katsuura
    Now processing: city number 108, name kodiak
    Now processing: city number 109, name voi
    Now processing: city number 110, name broome
    Now processing: city number 111, name lasa
    Now processing: city number 112, name bonavista
    Now processing: city number 113, name yellowknife
    Now processing: city number 114, name oildale
    Now processing: city number 115, name berlin
    Now processing: city number 116, name dikson
    Now processing: city number 117, name san carlos
    Now processing: city number 118, name bredasdorp
    Now processing: city number 119, name gardez
    Now processing: city number 120, name zhigansk
    Now processing: city number 121, name aklavik
    Now processing: city number 122, name cherskiy
    Now processing: city number 123, name constitucion
    Now processing: city number 124, name geraldton
    Now processing: city number 125, name grand river south east
    There is no city grand river south east in openweathermap database
    Now processing: city number 126, name merauke
    Now processing: city number 127, name saint george
    Now processing: city number 128, name norman wells
    Now processing: city number 129, name khatanga
    Now processing: city number 130, name namatanai
    Now processing: city number 131, name half moon bay
    Now processing: city number 132, name bluff
    Now processing: city number 133, name narsaq
    Now processing: city number 134, name kharsia
    Now processing: city number 135, name port elizabeth
    Now processing: city number 136, name mount gambier
    Now processing: city number 137, name salalah
    Now processing: city number 138, name panguna
    Now processing: city number 139, name barrow
    Now processing: city number 140, name grozesti
    Now processing: city number 141, name tsihombe
    There is no city tsihombe in openweathermap database
    Now processing: city number 142, name rawannawi
    There is no city rawannawi in openweathermap database
    Now processing: city number 143, name khao yoi
    Now processing: city number 144, name broken hill
    Now processing: city number 145, name busselton
    Now processing: city number 146, name gidole
    Now processing: city number 147, name lazarev
    Now processing: city number 148, name los llanos de aridane
    Now processing: city number 149, name butaritari
    Now processing: city number 150, name qaqortoq
    Now processing: city number 151, name severo-kurilsk
    Now processing: city number 152, name tabialan
    There is no city tabialan in openweathermap database
    Now processing: city number 153, name aasiaat
    Now processing: city number 154, name flinders
    Now processing: city number 155, name grocka
    Now processing: city number 156, name kruisfontein
    Now processing: city number 157, name ngaoundere
    Now processing: city number 158, name acapulco
    Now processing: city number 159, name vardo
    Now processing: city number 160, name rockland
    Now processing: city number 161, name saldanha
    Now processing: city number 162, name lovozero
    Now processing: city number 163, name hilo
    Now processing: city number 164, name skibbereen
    Now processing: city number 165, name kahului
    Now processing: city number 166, name hambantota
    Now processing: city number 167, name lebu
    Now processing: city number 168, name tabou
    Now processing: city number 169, name paamiut
    Now processing: city number 170, name ust-kuyga
    Now processing: city number 171, name sao filipe
    Now processing: city number 172, name cabedelo
    Now processing: city number 173, name hofn
    Now processing: city number 174, name shingu
    Now processing: city number 175, name douentza
    Now processing: city number 176, name mokrousovo
    Now processing: city number 177, name praia da vitoria
    Now processing: city number 178, name pisco
    Now processing: city number 179, name qaanaaq
    Now processing: city number 180, name hami
    Now processing: city number 181, name avera
    Now processing: city number 182, name muros
    Now processing: city number 183, name vilyuysk
    Now processing: city number 184, name tiksi
    Now processing: city number 185, name longyearbyen
    Now processing: city number 186, name wau
    There is no city wau in openweathermap database
    Now processing: city number 187, name klaksvik
    Now processing: city number 188, name hervey bay
    Now processing: city number 189, name vila franca do campo
    Now processing: city number 190, name ribeira grande
    Now processing: city number 191, name kavaratti
    Now processing: city number 192, name kaitangata
    Now processing: city number 193, name cidreira
    Now processing: city number 194, name ilinskiy
    Now processing: city number 195, name bambous virieux
    Now processing: city number 196, name port augusta
    Now processing: city number 197, name qobustan
    Now processing: city number 198, name orange walk
    Now processing: city number 199, name cockburn harbour
    There is no city cockburn harbour in openweathermap database
    Now processing: city number 200, name nador
    Now processing: city number 201, name belushya guba
    There is no city belushya guba in openweathermap database
    Now processing: city number 202, name marcona
    There is no city marcona in openweathermap database
    Now processing: city number 203, name mindyak
    Now processing: city number 204, name samusu
    There is no city samusu in openweathermap database
    Now processing: city number 205, name pemba
    Now processing: city number 206, name tual
    Now processing: city number 207, name chipinge
    Now processing: city number 208, name mitsamiouli
    Now processing: city number 209, name palmer
    Now processing: city number 210, name benjamin constant
    Now processing: city number 211, name port blair
    Now processing: city number 212, name moroni
    Now processing: city number 213, name itupiranga
    Now processing: city number 214, name baruun-urt
    Now processing: city number 215, name waingapu
    Now processing: city number 216, name ottawa
    Now processing: city number 217, name balkanabat
    Now processing: city number 218, name ormara
    Now processing: city number 219, name yuanping
    Now processing: city number 220, name masindi
    Now processing: city number 221, name farmington
    Now processing: city number 222, name saleaula
    There is no city saleaula in openweathermap database
    Now processing: city number 223, name redcar
    Now processing: city number 224, name mount pleasant
    Now processing: city number 225, name port hardy
    Now processing: city number 226, name mendoza
    Now processing: city number 227, name novomykolayivka
    Now processing: city number 228, name ust-maya
    Now processing: city number 229, name yarmouth
    Now processing: city number 230, name dunedin
    Now processing: city number 231, name chhoti sadri
    Now processing: city number 232, name berlevag
    Now processing: city number 233, name luba
    Now processing: city number 234, name adrar
    Now processing: city number 235, name lagoa
    Now processing: city number 236, name perth
    Now processing: city number 237, name pirajui
    Now processing: city number 238, name erzin
    Now processing: city number 239, name caravelas
    Now processing: city number 240, name victoria
    Now processing: city number 241, name deputatskiy
    Now processing: city number 242, name harper
    Now processing: city number 243, name chicama
    Now processing: city number 244, name marsa matruh
    Now processing: city number 245, name olafsvik
    There is no city olafsvik in openweathermap database
    Now processing: city number 246, name pedernales
    Now processing: city number 247, name vaitupu
    There is no city vaitupu in openweathermap database
    Now processing: city number 248, name anloga
    Now processing: city number 249, name lorengau
    Now processing: city number 250, name lithakia
    Now processing: city number 251, name dzhusaly
    There is no city dzhusaly in openweathermap database
    Now processing: city number 252, name kulhudhuffushi
    Now processing: city number 253, name bar harbor
    Now processing: city number 254, name evansville
    Now processing: city number 255, name warrnambool
    Now processing: city number 256, name atuona
    Now processing: city number 257, name candolim
    Now processing: city number 258, name aswan
    Now processing: city number 259, name hoa binh
    Now processing: city number 260, name yar-sale
    Now processing: city number 261, name zverinogolovskoye
    Now processing: city number 262, name suluq
    Now processing: city number 263, name nalut
    Now processing: city number 264, name golden
    Now processing: city number 265, name bongandanga
    Now processing: city number 266, name caarapo
    Now processing: city number 267, name tynset
    Now processing: city number 268, name port lincoln
    Now processing: city number 269, name bengkulu
    There is no city bengkulu in openweathermap database
    Now processing: city number 270, name arraial do cabo
    Now processing: city number 271, name temnikov
    Now processing: city number 272, name portland
    Now processing: city number 273, name norrtalje
    Now processing: city number 274, name zyryanka
    Now processing: city number 275, name kalakamati
    Now processing: city number 276, name namibe
    Now processing: city number 277, name papasquiaro
    There is no city papasquiaro in openweathermap database
    Now processing: city number 278, name lamar
    Now processing: city number 279, name guelengdeng
    There is no city guelengdeng in openweathermap database
    Now processing: city number 280, name sitio novo do tocantins
    There is no city sitio novo do tocantins in openweathermap database
    Now processing: city number 281, name gushikawa
    Now processing: city number 282, name amderma
    There is no city amderma in openweathermap database
    Now processing: city number 283, name airai
    Now processing: city number 284, name ponta delgada
    Now processing: city number 285, name rawson
    Now processing: city number 286, name buraydah
    Now processing: city number 287, name lavrentiya
    Now processing: city number 288, name sorvag
    There is no city sorvag in openweathermap database
    Now processing: city number 289, name bandarbeyla
    Now processing: city number 290, name taboshar
    Now processing: city number 291, name hithadhoo
    Now processing: city number 292, name yeppoon
    Now processing: city number 293, name faanui
    Now processing: city number 294, name boa vista
    Now processing: city number 295, name heihe
    Now processing: city number 296, name port-cartier
    Now processing: city number 297, name bedburg
    Now processing: city number 298, name linxia
    Now processing: city number 299, name alamogordo
    Now processing: city number 300, name betul
    Now processing: city number 301, name lompoc
    Now processing: city number 302, name agstafa
    There is no city agstafa in openweathermap database
    Now processing: city number 303, name gizo
    Now processing: city number 304, name northam
    Now processing: city number 305, name cayenne
    Now processing: city number 306, name takhli
    Now processing: city number 307, name port hawkesbury
    Now processing: city number 308, name lolua
    There is no city lolua in openweathermap database
    Now processing: city number 309, name touros
    Now processing: city number 310, name lerwick
    Now processing: city number 311, name vestmannaeyjar
    Now processing: city number 312, name bathsheba
    Now processing: city number 313, name tuatapere
    Now processing: city number 314, name raudeberg
    Now processing: city number 315, name attawapiskat
    There is no city attawapiskat in openweathermap database
    Now processing: city number 316, name pucara
    Now processing: city number 317, name flin flon
    Now processing: city number 318, name bud
    Now processing: city number 319, name vitim
    Now processing: city number 320, name nabire
    Now processing: city number 321, name arman
    Now processing: city number 322, name paso blanco
    Now processing: city number 323, name san cristobal
    Now processing: city number 324, name guerrero negro
    Now processing: city number 325, name arlit
    Now processing: city number 326, name port macquarie
    Now processing: city number 327, name vilhena
    Now processing: city number 328, name inyonga
    Now processing: city number 329, name marsabit
    Now processing: city number 330, name hunza
    There is no city hunza in openweathermap database
    Now processing: city number 331, name vila velha
    Now processing: city number 332, name jaunjelgava
    Now processing: city number 333, name agutaya
    Now processing: city number 334, name maragogi
    Now processing: city number 335, name mayo
    Now processing: city number 336, name fortuna
    Now processing: city number 337, name balsas
    Now processing: city number 338, name uk
    There is no city uk in openweathermap database
    Now processing: city number 339, name ambilobe
    Now processing: city number 340, name auray
    Now processing: city number 341, name presidencia roque saenz pena
    Now processing: city number 342, name vostok
    Now processing: city number 343, name vidim
    Now processing: city number 344, name marawi
    Now processing: city number 345, name aykhal
    Now processing: city number 346, name nha trang
    Now processing: city number 347, name puerto carreno
    Now processing: city number 348, name montrose
    Now processing: city number 349, name saskylakh
    Now processing: city number 350, name xiaoshi
    Now processing: city number 351, name iqaluit
    Now processing: city number 352, name gigmoto
    Now processing: city number 353, name chagda
    There is no city chagda in openweathermap database
    Now processing: city number 354, name lander
    Now processing: city number 355, name san lawrenz
    Now processing: city number 356, name mana
    Now processing: city number 357, name axim
    Now processing: city number 358, name alice town
    Now processing: city number 359, name poum
    Now processing: city number 360, name balyaga
    Now processing: city number 361, name neunkirchen
    Now processing: city number 362, name husavik
    Now processing: city number 363, name vung tau
    Now processing: city number 364, name anisoc
    Now processing: city number 365, name morgan city
    Now processing: city number 366, name beisfjord
    Now processing: city number 367, name cabo san lucas
    Now processing: city number 368, name namwala
    Now processing: city number 369, name nouadhibou
    Now processing: city number 370, name tabiauea
    There is no city tabiauea in openweathermap database
    Now processing: city number 371, name gao
    Now processing: city number 372, name tura
    Now processing: city number 373, name ust-tsilma
    Now processing: city number 374, name lamu
    Now processing: city number 375, name umm lajj
    Now processing: city number 376, name lentekhi
    Now processing: city number 377, name lyngseidet
    Now processing: city number 378, name kaeo
    Now processing: city number 379, name prince rupert
    Now processing: city number 380, name louisbourg
    There is no city louisbourg in openweathermap database
    Now processing: city number 381, name ilulissat
    Now processing: city number 382, name ngukurr
    There is no city ngukurr in openweathermap database
    Now processing: city number 383, name alberton
    Now processing: city number 384, name mizdah
    Now processing: city number 385, name tomatlan
    Now processing: city number 386, name hanson
    Now processing: city number 387, name spasskoye
    Now processing: city number 388, name sisophon
    Now processing: city number 389, name saginaw
    Now processing: city number 390, name fare
    Now processing: city number 391, name henties bay
    Now processing: city number 392, name chumikan
    Now processing: city number 393, name talcahuano
    Now processing: city number 394, name lebanon
    Now processing: city number 395, name ondorhaan
    There is no city ondorhaan in openweathermap database
    Now processing: city number 396, name petropavlovsk-kamchatskiy
    Now processing: city number 397, name comodoro rivadavia
    Now processing: city number 398, name at-bashi
    Now processing: city number 399, name calama
    Now processing: city number 400, name canguaretama
    Now processing: city number 401, name muroto
    Now processing: city number 402, name araouane
    Now processing: city number 403, name kurchum
    Now processing: city number 404, name ayan
    Now processing: city number 405, name tateyama
    Now processing: city number 406, name grand-lahou
    Now processing: city number 407, name rantauprapat
    Now processing: city number 408, name otradnoye
    Now processing: city number 409, name pietarsaari
    There is no city pietarsaari in openweathermap database
    Now processing: city number 410, name omboue
    Now processing: city number 411, name guanica
    Now processing: city number 412, name itarema
    Now processing: city number 413, name lamas
    Now processing: city number 414, name mao
    Now processing: city number 415, name nuuk
    Now processing: city number 416, name nogales
    Now processing: city number 417, name huangmei
    Now processing: city number 418, name belmonte
    Now processing: city number 419, name kawalu
    Now processing: city number 420, name la ronge
    Now processing: city number 421, name navrongo
    Now processing: city number 422, name taltal
    Now processing: city number 423, name havre-saint-pierre
    Now processing: city number 424, name dickson
    Now processing: city number 425, name brae
    Now processing: city number 426, name sunndalsora
    Now processing: city number 427, name puerto colombia
    Now processing: city number 428, name zhoucheng
    Now processing: city number 429, name altay
    Now processing: city number 430, name letka
    Now processing: city number 431, name kieta
    Now processing: city number 432, name havoysund
    Now processing: city number 433, name coripata
    Now processing: city number 434, name kropotkin
    Now processing: city number 435, name kalabo
    Now processing: city number 436, name andapa
    Now processing: city number 437, name kavieng
    Now processing: city number 438, name kloulklubed
    Now processing: city number 439, name sur
    Now processing: city number 440, name okha
    Now processing: city number 441, name yuancheng
    There is no city yuancheng in openweathermap database
    Now processing: city number 442, name russell
    Now processing: city number 443, name tilichiki
    Now processing: city number 444, name shelburne
    Now processing: city number 445, name tautira
    Now processing: city number 446, name takhtamygda
    Now processing: city number 447, name ulaanbaatar
    Now processing: city number 448, name karratha
    Now processing: city number 449, name champerico
    Now processing: city number 450, name litoral del san juan
    There is no city litoral del san juan in openweathermap database
    Now processing: city number 451, name yurla
    Now processing: city number 452, name sahrak
    There is no city sahrak in openweathermap database
    Now processing: city number 453, name stoyba
    There is no city stoyba in openweathermap database
    Now processing: city number 454, name willowmore
    Now processing: city number 455, name povolzhskiy
    Now processing: city number 456, name goderich
    Now processing: city number 457, name karakol
    Now processing: city number 458, name turan
    Now processing: city number 459, name richards bay
    Now processing: city number 460, name manaure
    Now processing: city number 461, name issia
    Now processing: city number 462, name rodrigues alves
    Now processing: city number 463, name upernavik
    Now processing: city number 464, name honningsvag
    Now processing: city number 465, name korla
    There is no city korla in openweathermap database
    Now processing: city number 466, name rapid valley
    Now processing: city number 467, name nanortalik
    Now processing: city number 468, name shimanovsk
    Now processing: city number 469, name el prat de llobregat
    Now processing: city number 470, name berestechko
    Now processing: city number 471, name mehamn
    Now processing: city number 472, name corinto
    Now processing: city number 473, name sinnamary
    Now processing: city number 474, name fairbanks
    Now processing: city number 475, name artyom
    Now processing: city number 476, name chalus
    Now processing: city number 477, name waipawa
    Now processing: city number 478, name upata
    Now processing: city number 479, name llanes
    Now processing: city number 480, name mentougou
    Now processing: city number 481, name houma
    Now processing: city number 482, name townsville
    Now processing: city number 483, name bay roberts
    Now processing: city number 484, name noumea
    Now processing: city number 485, name vao
    Now processing: city number 486, name hohhot
    Now processing: city number 487, name najran
    Now processing: city number 488, name chesma
    Now processing: city number 489, name manaus
    Now processing: city number 490, name westport
    Now processing: city number 491, name dhidhdhoo
    Now processing: city number 492, name camargo
    Now processing: city number 493, name betioky
    There is no city betioky in openweathermap database
    Now processing: city number 494, name sitka
    Now processing: city number 495, name samarai
    Now processing: city number 496, name san vicente
    Now processing: city number 497, name tangkak
    Now processing: city number 498, name iisalmi
    Now processing: city number 499, name tutoia
    Now processing: city number 500, name port keats
    Now processing: city number 501, name victor harbor
    Now processing: city number 502, name souillac
    Now processing: city number 503, name kuytun
    Now processing: city number 504, name haysville
    Now processing: city number 505, name valdosta
    Now processing: city number 506, name kuantan
    Now processing: city number 507, name kita
    Now processing: city number 508, name telma
    Now processing: city number 509, name menomonie
    Now processing: city number 510, name nzerekore
    Now processing: city number 511, name coquimbo
    Now processing: city number 512, name martapura
    Now processing: city number 513, name yanan
    There is no city yanan in openweathermap database
    Now processing: city number 514, name honiara
    Now processing: city number 515, name margaritas
    Now processing: city number 516, name vernon
    Now processing: city number 517, name middle island
    Now processing: city number 518, name samagaltay
    Now processing: city number 519, name ahuimanu
    Now processing: city number 520, name mocambique
    There is no city mocambique in openweathermap database
    Now processing: city number 521, name gravdal
    Now processing: city number 522, name port moresby
    Now processing: city number 523, name san andres
    Now processing: city number 524, name sambava
    Now processing: city number 525, name eidsvag
    Now processing: city number 526, name caceres
    Now processing: city number 527, name saint-francois
    Now processing: city number 528, name srednekolymsk
    Now processing: city number 529, name fort nelson
    Now processing: city number 530, name meulaboh
    Now processing: city number 531, name rio gallegos
    Now processing: city number 532, name jiazi
    Now processing: city number 533, name dire
    Now processing: city number 534, name santa isabel do rio negro
    Now processing: city number 535, name zheleznodorozhnyy
    Now processing: city number 536, name matay
    Now processing: city number 537, name ghatsila
    Now processing: city number 538, name wenling
    Now processing: city number 539, name ko samui
    Now processing: city number 540, name villazon
    There is no city villazon in openweathermap database
    Now processing: city number 541, name mamallapuram
    Now processing: city number 542, name shirokiy
    Now processing: city number 543, name buariki
    There is no city buariki in openweathermap database
    Now processing: city number 544, name iguape
    Now processing: city number 545, name alta floresta
    Now processing: city number 546, name saint-pierre
    Now processing: city number 547, name okakarara
    Now processing: city number 548, name beringovskiy
    Now processing: city number 549, name poronaysk
    Now processing: city number 550, name bumba
    Now processing: city number 551, name manadhoo
    Now processing: city number 552, name puerto del rosario
    Now processing: city number 553, name santa isabel
    Now processing: city number 554, name tirumakudal narsipur
    Now processing: city number 555, name todos santos
    Now processing: city number 556, name monaco-ville
    Now processing: city number 557, name scottsbluff
    Now processing: city number 558, name gharghoda
    Now processing: city number 559, name christchurch
    Now processing: city number 560, name mareeba
    Now processing: city number 561, name yerbogachen
    Now processing: city number 562, name atar
    Now processing: city number 563, name wichian buri
    Now processing: city number 564, name balakhninskiy
    Now processing: city number 565, name chilliwack
    Now processing: city number 566, name orsha
    Now processing: city number 567, name naryan-mar
    Now processing: city number 568, name hobyo
    Now processing: city number 569, name anori
    Now processing: city number 570, name iracoubo
    Now processing: city number 571, name karpathos
    Now processing: city number 572, name kapit
    Now processing: city number 573, name voh
    Now processing: city number 574, name grindavik
    Now processing: city number 575, name kalemie
    Now processing: city number 576, name ati
    Now processing: city number 577, name ituni
    There is no city ituni in openweathermap database
    Now processing: city number 578, name camacha
    Now processing: city number 579, name yomitan
    There is no city yomitan in openweathermap database
    Now processing: city number 580, name mopipi
    Now processing: city number 581, name dzhebariki-khaya
    Now processing: city number 582, name santa cruz de tenerife
    Now processing: city number 583, name eureka
    Now processing: city number 584, name gbadolite
    Now processing: city number 585, name sandane
    Now processing: city number 586, name banda aceh
    Now processing: city number 587, name potiskum
    Now processing: city number 588, name manggar
    Now processing: city number 589, name olinda
    Now processing: city number 590, name saint-agapit
    Now processing: city number 591, name beaufort
    Now processing: city number 592, name mugur-aksy
    Now processing: city number 593, name salinopolis
    Now processing: city number 594, name shchors
    Now processing: city number 595, name dekar
    Now processing: city number 596, name mackay
    Now processing: city number 597, name puerto escondido
    Now processing: city number 598, name jacksonville
    Now processing: city number 599, name smithers
    Now processing: city number 600, name moyale
    Now processing: city number 601, name balkhash
    Now processing: city number 602, name rosarito
    Now processing: city number 603, name juneau
    Now processing: city number 604, name tipuani
    Now processing: city number 605, name sorland
    Now processing: city number 606, name isla aguada
    Now processing: city number 607, name dakar
    Now processing: city number 608, name herat
    Now processing: city number 609, name ciras
    There is no city ciras in openweathermap database
    Now processing: city number 610, name bara
    Now processing: city number 611, name santa cruz cabralia
    Now processing: city number 612, name garowe
    Now processing: city number 613, name otukpo
    Now processing: city number 614, name manokwari
    Now processing: city number 615, name kaduqli
    Now processing: city number 616, name paranaiba
    Now processing: city number 617, name trairi
    Now processing: city number 618, name belogorsk
    Now processing: city number 619, name limbang
    Now processing: city number 620, name longlac
    There is no city longlac in openweathermap database
    Now processing: city number 621, name saint anthony
    Now processing: city number 622, name alice springs
    Now processing: city number 623, name ambovombe
    Now processing: city number 624, name dongfeng
    Now processing: city number 625, name kishtwar
    Now processing: city number 626, name karauzyak
    There is no city karauzyak in openweathermap database
    Now processing: city number 627, name nyaunglebin
    Now processing: city number 628, name moose factory
    Now processing: city number 629, name rezekne
    Now processing: city number 630, name college
    Now processing: city number 631, name sabha
    Now processing: city number 632, name hotarele
    Now processing: city number 633, name araguaina
    Now processing: city number 634, name rock springs
    Now processing: city number 635, name abu zabad
    Now processing: city number 636, name ahtopol
    Now processing: city number 637, name pedasi
    Now processing: city number 638, name mount isa
    Now processing: city number 639, name verkh-suetka
    Now processing: city number 640, name jalu
    Now processing: city number 641, name joensuu
    Now processing: city number 642, name sartell
    Now processing: city number 643, name dubbo
    Now processing: city number 644, name pereslavl-zalesskiy
    Now processing: city number 645, name xiaoyi
    Now processing: city number 646, name sao miguel do araguaia
    Now processing: city number 647, name acari
    Now processing: city number 648, name tecoanapa
    Now processing: city number 649, name lipin bor
    Now processing: city number 650, name solnechnyy
    Now processing: city number 651, name nizhneyansk
    There is no city nizhneyansk in openweathermap database
    Now processing: city number 652, name hualmay
    Now processing: city number 653, name kumphawapi
    Now processing: city number 654, name lakatoro
    Now processing: city number 655, name jiddah
    There is no city jiddah in openweathermap database
    Now processing: city number 656, name mouzakion
    There is no city mouzakion in openweathermap database
    Now processing: city number 657, name quatre cocos
    Now processing: city number 658, name nishihara
    Now processing: city number 659, name toliary
    There is no city toliary in openweathermap database
    Now processing: city number 660, name maningrida
    Now processing: city number 661, name aneho
    Now processing: city number 662, name rabo de peixe
    Now processing: city number 663, name dagda
    Now processing: city number 664, name sao jose da coroa grande
    Now processing: city number 665, name maykain
    There is no city maykain in openweathermap database
    Now processing: city number 666, name andros town
    Now processing: city number 667, name alyangula
    Now processing: city number 668, name dargaville
    Now processing: city number 669, name kachiry
    Now processing: city number 670, name mundo nuevo
    Now processing: city number 671, name bamiantong
    Now processing: city number 672, name umzimvubu
    There is no city umzimvubu in openweathermap database
    Now processing: city number 673, name arzgir
    Now processing: city number 674, name sholokhove
    Now processing: city number 675, name cheuskiny
    There is no city cheuskiny in openweathermap database
    Now processing: city number 676, name santa rosa
    Now processing: city number 677, name khani
    Now processing: city number 678, name pringsewu
    Now processing: city number 679, name siguiri
    Now processing: city number 680, name turkistan
    There is no city turkistan in openweathermap database
    Now processing: city number 681, name tumannyy
    There is no city tumannyy in openweathermap database
    Now processing: city number 682, name tocopilla
    Now processing: city number 683, name kupang
    Now processing: city number 684, name intipuca
    Now processing: city number 685, name alofi
    Now processing: city number 686, name turukhansk
    Now processing: city number 687, name nemuro
    Now processing: city number 688, name bull savanna
    Now processing: city number 689, name tivaouane
    Now processing: city number 690, name codrington
    Now processing: city number 691, name aksha
    Now processing: city number 692, name reefton
    There is no city reefton in openweathermap database
    Now processing: city number 693, name maceio
    Now processing: city number 694, name cunha
    Now processing: city number 695, name kurilsk
    Now processing: city number 696, name dubno
    Now processing: city number 697, name jijiga
    Now processing: city number 698, name pervomayskoye
    Now processing: city number 699, name tynaarlo
    Now processing: city number 700, name limulunga
    Now processing: city number 701, name noyabrsk
    Now processing: city number 702, name saint-georges
    Now processing: city number 703, name bilibino
    Now processing: city number 704, name saint-alexandre
    Now processing: city number 705, name canico
    Now processing: city number 706, name kumluca
    Now processing: city number 707, name krasnoarmeysk
    Now processing: city number 708, name abha
    Now processing: city number 709, name kiruna
    Now processing: city number 710, name koscierzyna
    Now processing: city number 711, name birao
    Now processing: city number 712, name nioro
    Now processing: city number 713, name nhulunbuy
    Now processing: city number 714, name galle
    Now processing: city number 715, name pompeu
    Now processing: city number 716, name xining
    Now processing: city number 717, name koster
    Now processing: city number 718, name austin
    Now processing: city number 719, name kalmunai
    Now processing: city number 720, name andilamena
    Now processing: city number 721, name seymchan
    Now processing: city number 722, name maha sarakham
    Now processing: city number 723, name mangrol
    Now processing: city number 724, name suntar
    Now processing: city number 725, name mondeville
    Now processing: city number 726, name oriximina
    Now processing: city number 727, name komsomolskiy
    Now processing: city number 728, name karamay
    There is no city karamay in openweathermap database
    Now processing: city number 729, name ambanja
    Now processing: city number 730, name pitimbu
    Now processing: city number 731, name we
    There is no city we in openweathermap database
    Now processing: city number 732, name wana
    Now processing: city number 733, name huesca
    Now processing: city number 734, name srivardhan
    Now processing: city number 735, name zielona gora
    Now processing: city number 736, name asilah
    There is no city asilah in openweathermap database
    Now processing: city number 737, name tianmen
    Now processing: city number 738, name cairo
    Now processing: city number 739, name umm kaddadah
    Now processing: city number 740, name canutama
    Now processing: city number 741, name san quintin
    Now processing: city number 742, name erdemli
    There is no city erdemli in openweathermap database
    Now processing: city number 743, name leningradskiy
    Now processing: city number 744, name chizhou
    Now processing: city number 745, name ozgon
    There is no city ozgon in openweathermap database
    Now processing: city number 746, name nazare
    There is no city nazare in openweathermap database
    Now processing: city number 747, name kidal
    Now processing: city number 748, name evensk
    Now processing: city number 749, name teya
    Now processing: city number 750, name uitenhage
    Now processing: city number 751, name salym
    Now processing: city number 752, name esmeraldas
    Now processing: city number 753, name roura
    Now processing: city number 754, name port-gentil
    Now processing: city number 755, name along
    Now processing: city number 756, name torma
    Now processing: city number 757, name guilin
    Now processing: city number 758, name thoen
    Now processing: city number 759, name saravan
    Now processing: city number 760, name artyk
    There is no city artyk in openweathermap database
    Now processing: city number 761, name mocuba
    Now processing: city number 762, name elizabeth city
    Now processing: city number 763, name burica
    There is no city burica in openweathermap database
    Now processing: city number 764, name mariental
    Now processing: city number 765, name crnici
    Now processing: city number 766, name sidi ali
    Now processing: city number 767, name coihaique
    Now processing: city number 768, name yen bai
    Now processing: city number 769, name morondava
    Now processing: city number 770, name la rioja
    Now processing: city number 771, name aflu
    There is no city aflu in openweathermap database
    Now processing: city number 772, name formoso do araguaia
    There is no city formoso do araguaia in openweathermap database
    74 cities were skipped
    698 cities data was captured


**Create a DataFrame based on Requests data**


```python
dframe_json = pd.DataFrame(all_requests_data)
final_req_df = dframe_json.transpose()
final_req_df.columns = ['Country', 'Latitude', 'Temperature(F)', 'Humidity(%)', 'Cloudiness(%)', 'Wind Speed(MpH)',
                        'Date']
print(final_req_df.count())
final_req_df.head()
```

    Country            698
    Latitude           698
    Temperature(F)     698
    Humidity(%)        698
    Cloudiness(%)      698
    Wind Speed(MpH)    698
    Date               698
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
      <th>Aasiaat</th>
      <td>GL</td>
      <td>68.71</td>
      <td>3.69</td>
      <td>100</td>
      <td>44</td>
      <td>6.64</td>
      <td>1522179860</td>
    </tr>
    <tr>
      <th>Abalak</th>
      <td>NE</td>
      <td>15.46</td>
      <td>80.1</td>
      <td>20</td>
      <td>8</td>
      <td>13.24</td>
      <td>1522179821</td>
    </tr>
    <tr>
      <th>Abha</th>
      <td>SA</td>
      <td>18.22</td>
      <td>64.4</td>
      <td>82</td>
      <td>0</td>
      <td>3.36</td>
      <td>1522177200</td>
    </tr>
    <tr>
      <th>Abu Zabad</th>
      <td>SD</td>
      <td>12.35</td>
      <td>73.57</td>
      <td>47</td>
      <td>0</td>
      <td>4.97</td>
      <td>1522180002</td>
    </tr>
    <tr>
      <th>Acapulco</th>
      <td>MX</td>
      <td>16.86</td>
      <td>82.4</td>
      <td>65</td>
      <td>40</td>
      <td>12.75</td>
      <td>1522176180</td>
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
        pass
        
print(f'{absent_cities_owm} cities were skipped')
print(f'{len(all_owm_data)} cities data was captured')
```

    There is no city mys shmidta in openweathermap database
    There is no city taolanaro in openweathermap database
    There is no city akyab in openweathermap database
    There is no city barentsburg in openweathermap database
    There is no city illoqqortoormiut in openweathermap database
    There is no city sentyabrskiy in openweathermap database
    There is no city skagastrond in openweathermap database
    There is no city berbera in openweathermap database
    There is no city palabuhanratu in openweathermap database
    There is no city grand river south east in openweathermap database
    There is no city tsihombe in openweathermap database
    There is no city rawannawi in openweathermap database
    There is no city tabialan in openweathermap database
    There is no city wau in openweathermap database
    There is no city cockburn harbour in openweathermap database
    There is no city belushya guba in openweathermap database
    There is no city marcona in openweathermap database
    There is no city samusu in openweathermap database
    There is no city saleaula in openweathermap database
    There is no city olafsvik in openweathermap database
    There is no city vaitupu in openweathermap database
    There is no city dzhusaly in openweathermap database
    There is no city bengkulu in openweathermap database
    There is no city papasquiaro in openweathermap database
    There is no city guelengdeng in openweathermap database
    There is no city sitio novo do tocantins in openweathermap database
    There is no city amderma in openweathermap database
    There is no city sorvag in openweathermap database
    There is no city agstafa in openweathermap database
    There is no city lolua in openweathermap database
    There is no city attawapiskat in openweathermap database
    There is no city hunza in openweathermap database
    There is no city uk in openweathermap database
    There is no city chagda in openweathermap database
    There is no city tabiauea in openweathermap database
    There is no city louisbourg in openweathermap database
    There is no city ngukurr in openweathermap database
    There is no city ondorhaan in openweathermap database
    There is no city pietarsaari in openweathermap database
    There is no city yuancheng in openweathermap database
    There is no city litoral del san juan in openweathermap database
    There is no city sahrak in openweathermap database
    There is no city stoyba in openweathermap database
    There is no city korla in openweathermap database
    There is no city betioky in openweathermap database
    There is no city yanan in openweathermap database
    There is no city mocambique in openweathermap database
    There is no city villazon in openweathermap database
    There is no city buariki in openweathermap database
    There is no city ituni in openweathermap database
    There is no city yomitan in openweathermap database
    There is no city ciras in openweathermap database
    There is no city longlac in openweathermap database
    There is no city karauzyak in openweathermap database
    There is no city nizhneyansk in openweathermap database
    There is no city jiddah in openweathermap database
    There is no city mouzakion in openweathermap database
    There is no city toliary in openweathermap database
    There is no city maykain in openweathermap database
    There is no city umzimvubu in openweathermap database
    There is no city cheuskiny in openweathermap database
    There is no city turkistan in openweathermap database
    There is no city tumannyy in openweathermap database
    There is no city reefton in openweathermap database
    There is no city karamay in openweathermap database
    There is no city we in openweathermap database
    There is no city asilah in openweathermap database
    There is no city erdemli in openweathermap database
    There is no city ozgon in openweathermap database
    There is no city nazare in openweathermap database
    There is no city artyk in openweathermap database
    There is no city burica in openweathermap database
    There is no city aflu in openweathermap database
    There is no city formoso do araguaia in openweathermap database
    74 cities were skipped
    698 cities data was captured


**Create a DataFrame based on OWM data**


```python
search = ['name', 'sys.country', 'coord.lat', 'main.temp','main.humidity', 'clouds.all', 'wind.speed', 'dt']
extracted_data = [city(*search) for city in all_owm_data]

final_owm_df = pd.DataFrame(extracted_data)
final_owm_df.columns = ['Name','Country', 'Latitude', 'Temperature(C)', 'Humidity(%)', 'Cloudiness(%)',
                        'Wind Speed(MpH)', 'Date']
print(final_owm_df.count())
final_owm_df.head()
```

    Name               698
    Country            698
    Latitude           698
    Temperature(C)     698
    Humidity(%)        698
    Cloudiness(%)      698
    Wind Speed(MpH)    698
    Date               698
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
      <td>Butterworth</td>
      <td>MY</td>
      <td>5.40</td>
      <td>26.51</td>
      <td>88</td>
      <td>75</td>
      <td>2.60</td>
      <td>1522177200</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Furano</td>
      <td>JP</td>
      <td>43.28</td>
      <td>0.72</td>
      <td>75</td>
      <td>32</td>
      <td>2.07</td>
      <td>1522180048</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Provideniya</td>
      <td>RU</td>
      <td>64.42</td>
      <td>-5.26</td>
      <td>100</td>
      <td>76</td>
      <td>1.77</td>
      <td>1522179986</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ponta do Sol</td>
      <td>BR</td>
      <td>-20.63</td>
      <td>24.52</td>
      <td>53</td>
      <td>8</td>
      <td>1.72</td>
      <td>1522179813</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Sint-Truiden</td>
      <td>BE</td>
      <td>50.82</td>
      <td>7.11</td>
      <td>100</td>
      <td>88</td>
      <td>2.10</td>
      <td>1522178700</td>
    </tr>
  </tbody>
</table>
</div>



**Temperature vs Latitude Plot**


```python
sns.set()
basedf = final_owm_df # DataFrame source for all plots can be easilly changed
current_date = datetime.datetime.now().date().strftime("%m/%d/%Y")
plt.figure(figsize=(10,7))
plt.scatter(basedf['Temperature(C)'], basedf['Latitude'], c=basedf['Temperature(C)'], s=30, cmap='coolwarm')
plt.ylabel('Latitude')
plt.xlabel('Temperature (C)')
plt.title(f'Temperature (C) vs. City Latitude ({current_date})')
plt.tight_layout()
```


![png](output_18_0.png)


**Latitude vs Humidity, Cloudiness and Wind Speed Plots**


```python
sns.set()
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
```




    Text(0.5,1,'City Latitude vs City Wind Speed (03/27/2018)')




![png](output_20_1.png)

