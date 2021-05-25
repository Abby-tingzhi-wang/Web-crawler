# Web-crawler
# download the table from a government public website

# install 
    ! pip install requests
    ! pip install bs4
    ! pip install lxml

# Prepare 
    import requests
    from bs4 import BeautifulSoup
    import csv
    import pandas as pd

# Method 1 The exported csv file is not align in colomns

# get data from page 1-10 
    for i in range (0,10,1):
    url='https://www.foreignarrangements.gov.au/public-register?search_api_fulltext=&field_search_filter_select=All&field_arrangement_date_time%5Bmin%5D=&field_arrangement_date_time%5Bmax%5D=&field_decision_date_time%5Bmin%5D=&field_decision_date_time%5Bmax%5D=&field_minister_decision_taxonomy=All&page={}'.format(i)
    html=requests.get(url)
    print(html.text)
    soup=BeautifulSoup(html.text,'lxml')
    
 # get data and clean data    
    lis=soup.select('div.overall-pr-item-wrap')
    for li in lis:
        title=li.select('div.pr-title')[0].text.strip().replace('Date foreign arrangement entered','').replace('\n','')
        print('Title:',title)
        local=li.select('div.pr-local-entity')[0].text.replace('State/Territory entity or other','').replace('\n','')
        print('local_entity:',local)
        foreign=li.select('div.pr-foreign-entity')[0].text.replace('Foreign entity','').replace('\n','')
        print('foreign_entity:',foreign)
      
 # write data into csv file
        with open('fra.csv','a',newline='') as f:
            writer=csv.writer(f,delimiter='|') 
            writer.writerows([title,local,foreign])
            
# Method 2 Store data in arrays

    rows = []
    rows.append(['title', 'date_entered','local_entity', 'foreign_entity'])

    for r in range (0,9,1):
    url='https://www.foreignarrangements.gov.au/public-register?search_api_fulltext=&field_search_filter_select=All&field_arrangement_date_time%5Bmin%5D=&field_arrangement_date_time%5Bmax%5D=&field_decision_date_time%5Bmin%5D=&field_decision_date_time%5Bmax%5D=&field_minister_decision_taxonomy=All&page={}'.format(r)
    html=requests.get(url)
    soup=BeautifulSoup(html.text,'lxml')
       
   # get data 
    for i in soup.select('div.pr-title'):
        row=[]
        row.append(i.get_text())
        rows.append(row)
    
    for i,e in enumerate(soup.select('div.pr-title-sub-wrap')):
        index = i + 1
        rows[index].append(e.find('span').get_text())

    for i, e in enumerate(soup.select('div.pr-local-entity')):
        index = i + 1
        rows[index].append(e.get_text())

    for i, e in enumerate(soup.select('div.pr-foreign-entity')):
        index = i + 1
        rows[index].append(e.get_text())
        
# write data into csv file
    with open('777.csv','a', newline='') as f:
    writer=csv.writer(f,delimiter=',')
    writer.writerows(rows)

        
