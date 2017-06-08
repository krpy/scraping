# scraping

import sys
sys.path.append('/usr/lib/python2.7/dist-packages/')

#lib na scrapovani
from bs4 import BeautifulSoup

#dle verze pythonu se pouzije urllib2 nebo irllib.request
#import urllib2 
from six.moves import urllib
import urllib.request
import urllib.request  as urllib2 
import urllib.parse

import csv
import os
import re


from __future__ import print_function
from os.path import join, dirname, abspath
import xlrd
import pandas as pd
from lxml import html
import requests
import time


# set working directory:
#os.chdir("C://Users//Honza//Desktop")

# importing excel as dataframe, kam bych pak eventualne chtel vyplnovat hodnoty z toho webu
transfers = pd.read_excel("C://Users//Honza//Desktop//Trading.xls")

# changing the user - tak aby me ten web byl zpristupnen
url = "https://powerfultrader.eu/?p=5200"
headers = {}
headers['User-Agent'] = "Mozilla/5.0 (X11; Linux i686)"

# uz oteviram tu website
req = urllib.request.Request(url, headers=headers)
resp = urllib.request.urlopen(req)
soup = BeautifulSoup(resp, "lxml")

# tohle printne tu website, kde to po me chce vyplnit udaje.
print(soup)

# pripraveny kod na finalni potrebna data - bude jeste potreba udelat
meny = "EURUSD", "GBPUSD", "AUDUSD", "USDJPY", "NZDUSD", "USDCHF", "USDCAD"

soup.prettify()
soup.write
obchody = soup.findAll("div")
print(obchody)



# Takze tady se musime vratit na radek pred tim nez jsem zacal pracovat s headers tedy 43
# a je potreba si nejdriv otevrit ten web - prihlasit se - a potom teprve pokracovat ve scrapovani

# jsou asi dve cesty a nevim, jestli staci otevrit ten web, prihlasit se, a potom si ho otevrit znova pres to headers...


# nasel jsem tri pristupy jak na to:

# login tu nepublikuju :D
payload = {
    "username": "****",
    "password": "*****"
}

# prvni moznost nefunguje 
with requests.Session() as c:
    url = "https://powerfultrader.eu/wp-login.php?redirect_to=https%3A%2F%2Fpowerfultrader.eu%2F%3Fp%3D5200"
    c.post("https://powerfultrader.eu/wp-login.php?redirect_to=https%3A%2F%2Fpowerfultrader.eu%2F%3Fp%3D5200", data =payload)
    r = c.get("https://powerfultrader.eu/?p=5200")
    print(r)
# vytiskne to <Response [200]>


# tenhle pristup by snad mel jit. Chyba je v tom tokenu, jen se musi nejak ulozit a pak predat do login_data a pak do funkce client.post
# set the referrer to the same URL as the login page:
URL = "https://powerfultrader.eu/wp-login.php?redirect_to=https%3A%2F%2Fpowerfultrader.eu%2F%3Fp%3D5200"
client = requests.session()
# Retrieve the CSRF token first - tohle krasne vytiskne ten token, ale nevim jak ho ulozit a pak ho predat do te funkce
for key in c.cookies.keys():
        print('%s: %s' % (key, c.cookies[key]))
        print(c.cookies[key])
csrftoken =  # tady je potreba ulozit token z predchozich tri radku

csrf = client.get(url).cookies['csrftoken'] #jeste ho zkousim ulozit jinak = taky nefunguje
login_data = dict(csrfmiddlewaretoken=csrftoken, username=EMAIL, password=PASSWORD, next='/')
r = client.post(URL, data=login_data, headers=dict(Referer=URL))





# treti cesta, ktera je uplne stejna jen se ten token uklada trochu jinak a chyba je dole
with requests.Session() as c:
    url = "https://powerfultrader.eu/wp-login.php?redirect_to=https%3A%2F%2Fpowerfultrader.eu%2F%3Fp%3D5200"
    USERname = "***"
    PASSword = "***"
    c.get(url)
    csrftoken = c.cookies['csrftoken']
    login_data = dict(csrfmiddlewaretoken=csrftoken, username=USERname, password=PASSword, next='/')
    c.post(url, data=login_data, headers={"Referer": "https://powerfultrader.eu/?p=5200"})
    page = c.get("https://powerfultrader.eu/?p=5200")
    print(page.content)
#KeyError: "name='csrftoken', domain=None, path=None"




#tohle vytiskne tu stranku, kde to po me chce prihlaseni = nefunguje to :D 
with requests.Session() as c:
    url = "https://powerfultrader.eu/wp-login.php?redirect_to=https%3A%2F%2Fpowerfultrader.eu%2F%3Fp%3D5200"
    USERname = "***"
    PASSword = "***"
    c.get(url)
    login_data = dict(csrfmiddlewaretoken="N%C3%A1v%C5%A1t%C4%9Bvn%C3%ADk_8132", username=USERname, password=PASSword, next='/')
    c.post(url, data=login_data, headers={"Referer": "https://powerfultrader.eu/?p=5200"})
    page = c.get("https://powerfultrader.eu/?p=5200")
    print(page.content)
    r = c.get("https://powerfultrader.eu/?p=5200")
    
    



#dalsi moznost ktera haze jinou chybu
with requests.Session(config={'verbose': sys.stderr}) as c:
    c.post('https://powerfultrader.eu/wp-login.php?redirect_to=https%3A%2F%2Fpowerfultrader.eu%2F%3Fp%3D5200', data=payload)
    r = c.get('https://powerfultrader.eu/?p=5200')
    print('sopier') in r.content

#TypeError: __init__() got an unexpected keyword argument 'config'
#---------------------------------------------------------------------------
#TypeError                                 Traceback (most recent call last)
#<ipython-input-66-28072e0c4828> in <module>()
#----> 1 with requests.Session(config={'verbose': sys.stderr}) as c:
#      2     c.post('https://powerfultrader.eu/wp-login.php?redirect_to=https%3A%2F%2Fpowerfultrader.eu%2F%3Fp%3D5200', data=payload)
#      3     r = c.get('https://powerfultrader.eu/?p=5200')
#      4     print('sopier') in r.content
#TypeError: __init__() got an unexpected keyword argument 'config'
