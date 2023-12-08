import requests
from bs4 import BeautifulSoup


def scrape_website(url):
        response = requests.get(url)
        if response.status_code == 200:
            soup = BeautifulSoup(response.text, 'html.parser')
            unique_div_texts = set() #it store all unique div values 
            for div in soup.find_all('div'):
                div_text = div.get_text(strip=True)
                if div_text:
                    unique_div_texts.add(div_text)
            cleaned_text = '\n'.join(unique_div_texts)
            return cleaned_text
        else:
            return f"Error: Unable to fetch data. Status code: {response.status_code}"
        
website_url = input("Enter Website Url: ")
website_data = scrape_website(website_url)

with open("website_data.txt", "w", encoding="utf-8") as file:
    file.write(website_data) 

#run if you have not added a api key also if you have windows then you can type in terminal - set OPENAI_API_KEY=sk-xxxx 
import os
os.environ["OPENAI_API_KEY"] = "Enter_Your_api_key"

from llama_index import VectorStoreIndex, SimpleDirectoryReader
""" I have used here llamma index because it help us to reduce token count for given text which is scrape from given website 
and then use vectorstore to convert textual data into vectors which is helpfull to retrive most simliar data into text.
react is chat engine for retrive the data from given text
"""
data = SimpleDirectoryReader(input_dir="C://Users/PRADEEP SAINI/Desktop/New folder").load_data()#paste the url of folder in which you have store your txt file
index = VectorStoreIndex.from_documents(data) 
chat_engine = index.as_chat_engine(chat_mode='react', verbose=True)
while(True):
     print("For exit press 'exit' ")
     txt_input = input("Enter the Question: ")
     if txt_input == 'exit':
          break
     response = chat_engine.chat(f'Use the tool to answer:{txt_input} ' )
     print(response)

##pradeep saini mca 
