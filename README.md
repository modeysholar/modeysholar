#!/usr/bin/env python
# coding: utf-8

# In[1]:


import requests
from bs4 import BeautifulSoup
text = requests.get('https://scholar.google.co.uk/citations?view_op=view_org&hl=en&org=9117984065169182779')


# In[2]:


source= text.text
soup= BeautifulSoup(source, 'html.parser')

links=[ ]
new_links= []
for h3_tag in soup.find_all("h3", class_= 'gs_ai_name'):
    a_tag= h3_tag.find('a')
    links= a_tag.attrs['href']
    new_link= 'https://scholar.google.co.uk'+ links

    print(new_link)
    print("-----------------------------------------")
    new_links.append(new_link)


# In[3]:


#!pip install fake-useragent

from fake_useragent import UserAgent
import csv
import time

Publication_links= []
Publication_content={}
All_content=[]
urls= new_links
user_agent = UserAgent()

## starting the loop
for url in urls:
    ## getting the reponse from the page using get method of requests module
    page = requests.get(url, headers={"user-agent": user_agent.chrome})
    html = page.text
    soup = BeautifulSoup(html, "html.parser")## creating BeautifulSoup object
    
    for item in soup.find_all('tr', class_ ='gsc_a_tr'): 
        path= item.find('a')
        pub_url= path['data-href']
        pub_urls='https://scholar.google.co.uk'+ pub_url
        Publication_links.append(pub_urls)
        #time.sleep(2)

        
          # Extracting each publication links
        for item in Publication_links:
            pages = requests.get(item) #, headers={"user-agent": user_agent.chrome})
            s= pages.text
            soup= BeautifulSoup(s, 'html.parser')
            #publication_link= soup.find('a', class_= 'gsc_vcd_title_link')
            
            for item in soup:
                publication_link=find_all('a', class_= 'gsc_vcd_title_link') 
                #path= item.find('a')
                #pub_url= path['data-href']
                puub_urls='https://scholar.google.co.uk'+ publication_link
                #Publication_links.append(puub_urls)
            
            if puub_urls is not None:
                Publication_content['title'] = publication_link.decode_contents()
                Publication_content['url'] = publication_link.get('href')
                Publication_content.append(Publication_content['url'])
print (Publication_content)
                
                #time.sleep(2)
    # Extracting the authors
            #for div in soup.find_all('div', class_='gs_scl'):
                #field= div.findChild('div', class_='gsc_vcd_field')
                #value= div.findChild('div', class_= 'gsc_vcd_value')
                #if field is None or value is None:
                    #continue
                #field_C= field.decode_contents()  
            
                #if field_C== 'Authors':
                    #Publication_content['Authors']= value.decode_contents()
                #elif field_C== 'Publication date':
                    #Publication_content['Published']= value.decode_contents()
                ##elif field_C== 'Publisher':
                    #Publication_content['Publisher']= value.decode_contents()
                #elif field_C== 'Description':
                    #description_div= div.findChildren('div', class_= 'gsh_csp')
                    #description_content= [x.decode_contents() for x in description_div]
                    #Publication_content['Description']= description_content
            
#All_content.append(Publication_content)
        
#print(All_content)  


# In[4]:


import csv 
with open('publications_list.csv','w') as csvfile: 
     writer = csv.writer(csvfile)
     for item in Publication_links: 
        writer.writerow([item])
