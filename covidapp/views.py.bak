from django.shortcuts import render

from django.shortcuts import render
from django.http import HttpResponse
from datetime import date
import requests
import json
from datetime import datetime,timedelta
previous_date = datetime.now() - timedelta(days=7)
dateurl="https://api.data.gov.hk/v2/filter?q=%7B%22resource%22%3A%22http%3A%2F%2Fwww.chp.gov.hk%2Ffiles%2Fmisc%2Foccupancy_of_quarantine_centres_eng.csv%22%2C%22section%22%3A1%2C%22format%22%3A%22json%22%7D"
  
response = requests.get(dateurl)
#print(response.status_code)
date=response.json()[-1]['As of date']
date_time_obj = datetime.strptime(date, '%d/%m/%Y')
has_data=(previous_date<date_time_obj)
specificdateurl ="https://api.data.gov.hk/v2/filter?q=%7B%22resource%22%3A%22http%3A%2F%2Fwww.chp.gov.hk%2Ffiles%2Fmisc%2Foccupancy_of_quarantine_centres_eng.csv%22%2C%22section%22%3A1%2C%22format%22%3A%22json%22%2C%22filters%22%3A%5B%5B1%2C%22eq%22%2C%5B%22"+date[0:2]+"%2F"+date[3:5]+"%2F"+date[6:]+"%22%5D%5D%5D%7D"

response2 = requests.get(specificdateurl)
#print(response2.json())

units_available=0
units_in_use=0
persons_quarantined=0
for i in range(len(response2.json())):
    units_in_use+=response2.json()[i]['Current unit in use']
    units_available+=response2.json()[i]['Ready to be used (unit)']
    persons_quarantined+=response2.json()[i]['Current person in use']
top3url="https://api.data.gov.hk/v2/filter?q=%7B%22resource%22%3A%22http%3A%2F%2Fwww.chp.gov.hk%2Ffiles%2Fmisc%2Foccupancy_of_quarantine_centres_eng.csv%22%2C%22section%22%3A1%2C%22format%22%3A%22json%22%2C%22sorts%22%3A%5B%5B8%2C%22desc%22%5D%5D%2C%22filters%22%3A%5B%5B1%2C%22eq%22%2C%5B%22"+date[0:2]+"%2F"+date[3:5]+"%2F"+date[6:]+"%22%5D%5D%5D%7D"
response3=requests.get(top3url)
centres=[]
for i in range(3):
    centres.append({"name":response3.json()[i]["Quarantine centres"],"units":response3.json()[i]["Ready to be used (unit)"]})

api2url="https://api.data.gov.hk/v2/filter?q=%7B%22resource%22%3A%22http%3A%2F%2Fwww.chp.gov.hk%2Ffiles%2Fmisc%2Fno_of_confines_by_types_in_quarantine_centres_eng.csv%22%2C%22section%22%3A1%2C%22format%22%3A%22json%22%2C%22filters%22%3A%5B%5B1%2C%22eq%22%2C%5B%22"+date[0:2]+"%2F"+date[3:5]+"%2F"+date[6:]+"%22%5D%5D%5D%7D"
response4=requests.get(api2url)

count_consistent=persons_quarantined==(response4.json()[-1]["Current number of close contacts of confirmed cases"]+response4.json()[-1]["Current number of non-close contacts"])
    


def dashboard(request):
    
    context={
    "connected":response.status_code,
    "has_data":has_data,
    "data":{
    "date":date,"units_in_use":units_in_use,
    "units_available":units_available,
    "persons_quarantined":persons_quarantined,
    "non_close_contacts":response4.json()[-1]["Current number of non-close contacts"],
    "count_consistent":count_consistent
    },
    "centres":centres
    }
    
    return render(request, 'dashb.html',context=context)