# Web-Scraping-Script

from bs4 import BeautifulSoup 
import requests
import pandas as pd
from time import sleep
import json
from selenium import webdriver
import re

key_people = pd.read_csv("C:\\Users\FOX1JUK\Documents\Web Scrape\VW_Dealership Key People List 29 Mar 2019_Summary.csv")
pop_time= pd.read_csv("C:\\Users\FOX1JUK\Documents\Web Scrape\VW_Popular_Times_Test.csv")
dealer_pop= pd.read_csv("C:\\Users\FOX1JUK\Documents\Web Scrape\VW_Popular_Times_Test_All_Dealerships.csv")

key_people.head()

key_people.iloc[2:,3]

##Logic for appending days to dealership times

counter = -1
list_count = -1
day = ["Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"]
date_list = dealer_pop.iloc[:,1].values.T.tolist()
dealer_list = dealer_pop.iloc[:,0].values.T.tolist()
for i in date_list:
    list_count = list_count + 1
    if dealer_list[list_count] == dealer_list[list_count-1]:
        if "AM" in i: 
            if "PM" in date_list[list_count-1]:
                counter = counter + 1
        print(day[counter]+" "+ i )
    elif dealer_list[list_count] != dealer_list[list_count-1]:
        counter = -1 
        if "AM" in i: 
            if "PM" in date_list[list_count-1]:
                counter = counter + 1
            print(day[counter]+" "+ i )
            
##Scrapes all the dealership popularity times
##Hashed because a lot of data 

driver = webdriver.Firefox()
for dealership in key_people.iloc[2:,3]: 
    driver.get("https://www.google.com/search?q="+ str(dealership))
    sleep(1)
    html = driver.page_source
    html = BeautifulSoup(html, "html.parser")
    for entry in html.find_all('div', {'class':'lubh-bar'}):
        list=[]
        list.append(dealership)
        list.append(entry)
        print(list)


#Searches google for popular times of dealerships

driver = webdriver.Firefox()
for dealership in key_people.iloc[2:,3]: 
    driver.get("https://www.google.com/search?q="+ str(dealership))
    list=[]
    sleep(1)
    html = driver.page_source
    html = BeautifulSoup(html, "html.parser")
    for entry in html.find_all('table', {'class':'WgFkxc'}):
        list.append(entry.text)
        print(list)
        
#Searches google for opening times of dealerships

driver = webdriver.Firefox()
for dealership in key_people.iloc[2:,3]: 
    driver.get("https://www.google.com/search?q="+ str(dealership))
    list=[]
    sleep(1)
    html = driver.page_source
    html = BeautifulSoup(html, "html.parser")
    for entry in html.find_all('table', {'class':'WgFkxc'}):
        list.append(str(dealership))
        list.append(entry.text)
        print(list)
        
df = pd.dataframe(np.array(list),columns = ['Dealership','Monday','Tuesday','Wednesday','Thursday','Friday','Saturday','Sunday'])
list = list.str.split(',',expand = True)
