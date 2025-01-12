## Extrating Web Data for Trade Show Prospecting

**Project description:** This project is code which I wrote and used throughout my time as a business developer/account manager at Infopro Digital. Often times, B2B trade shows will offer a functionality allowing you to download exhibitor data in an excel speadsheet. This is however not always the case, and it was common for sales people to take down infomation manually. This script allowed myself and my team to quickly and easily get the information needed for proper preparation of our trade show prospection.

### 1. Loading necessary packages

Here we are loading in all the necessary packages for this project.

```
import requests
from selenium import webdriver
from selenium.webdriver.common.by import By
from bs4 import BeautifulSoup
import time
import pandas as pd
import re
```

### 2. Creating python lists to store extracted data

```
speaker_names = []
speaker_countries = []
speaker_functions = []
speaker_cards = []
```

### 3. Seting up the web driver using selenium

```
driver_path = '../chromedriver'
driver = webdriver.Chrome(driver_path)
```

### 4. Loading our webpage

```
driver.get('https://vivatechnology.com/')
```

### 5. Getting the page source

Here I manually nagivate within our Google Chrome session to enter the username and password for the exhibitor account. I then navigated to the webpage where all of our exhibitor details are located and ensure all elements are loaded on the page before extracting the page source.

```
html = driver.page_source
soup = BeautifulSoup(html, 'html.parser')
```

### 6. Closing our selenium session

Now that the page source has been successful extracted and saved within our variables, I no longer need to keep my browser session open.

```
driver.quit()
```

### 7. Extracting data from contact cards

The details for each exhibitor was contained within a card layout. Here I am extracting all data within each card, which I can then further dissect to get the exact information needed from each one in an organised manner.

```
cards = soup.find_all('div', {'class': 'detail detail'})
```

### 8. Extracting speaker names

Here I am using a combination of a for loop and if statements to parse through our cards and extract the speaker names. The speaker names will be added one by one to the speaker name python list which we previously created.

```
for i in cards:
    if i is not None:
        soup = BeautifulSoup(str(i), 'html.parser')
        speaker_name = soup.find('h4', {'class': 'name bloc-accent name bloc-accent'})
        if speaker_name is not None:
            speaker_names.append(speaker_name.text.strip())
```

### 9. Extracting speaker countries

Like in our code above, I am using a combination of a for loop and if/else statements to extract speaker country data. I found it important to add 'None' to our list when data is null to ensure that our lists are of equal length. This is important for later when will combine these python lists into a pandas data frame.

```
for i in cards:
    if i is not None:
        soup = BeautifulSoup(str(i), 'html.parser')
        speaker_country = soup.find_all('h5', {'class': 'company company'})
        if len(speaker_country) == 0:
            speaker_countries.append('None')
        else:
            for i in speaker_country:
                if i is not None:
                    speaker_countries.append(i.text.strip())
```

### 10. Extracting speaker job functions

Here we are repeated the same process from above to extract the exhibitor job functions/titles.

```
for i in cards:
    if i is not None:
        soup = BeautifulSoup(str(i), 'html.parser')
        speaker_function = soup.find_all('h5', {'class': 'jobTitle jobTitle'})
        if len(speaker_function) == 0:
            print('empty')
            speaker_functions.append('None')
        else:
            for i in speaker_function:
                if i is not None:
                    speaker_functions.append(i.text.strip())
```

### 11. Combining our python lists

Our python lists are now being combined to be transformed into a pandas data frame.

```
data = list(zip(speaker_names, speaker_countries, speaker_functions))
```

### 12. Creating our pandas data frame

A pandas dataframe 'df' is created with the column titles 'Speakers', 'Company', and 'Job Titles'.

```
df = pd.DataFrame(data, columns=['Speakers', 'Company', 'Job Titles'])
```

### 13. Checking our data frame

This step is purely to check the first few rows of our new dataframe to check that it looks correct before proceeding.

```
df.head()
```

### 14. Saving our data as an excel file

The last step is saving our extracted exhibitor data in an excel file.

```
df.to_excel('Viva Technology Platform Partner Speakers.xlsx', index=False)
```
In a next project, I will demonstrate how I cross this exhibitor data with our client data in Salesforce to determine which exhibitors are current clients, new prospects, or disqualified from our sales pipe.
