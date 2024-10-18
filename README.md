# importing necessary modules

from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException
import pandas as pd
setting up a custom Chrome browser for testing, specifiying to the Chrome executable and ChromeDriver, then configuring the browser to use this custom setup. Once everything is initialized, it launches Chrome and navigates to the ESPN Cricinfo page.
chrome_test_path ="D:\\Webscraping\\chrome-win64\\chrome-win64\\chrome.exe"                                                    # custom chrome path (the testing Chrome)

chromedriver_path = "D:\\Webscraping\\chromedriver-win64\\chromedriver.exe"                                                    # chromedriver Path

# Set ChromeOptions to use the specific Chrome binary
chrome_options = Options()
chrome_options.binary_location = chrome_test_path

# Initialize the ChromeDriver with the custom binary and service
service = Service(executable_path=chromedriver_path)
driver = webdriver.Chrome(service=service, options=chrome_options)


website = 'https://www.espncricinfo.com/series/icc-men-s-t20-world-cup-2024-1411166/match-schedule-fixtures-and-results'
driver.get(website)

extracting specific match details from a webpage using Selenium. It retrieves:

- `team1`: The name of the first team.
- `team2`: The name of the second team.
- `results`: The match result.
- `loc`: The match location (after processing the text to extract the relevant part).
- `dates`: The date of the match.

Each element is found using its corresponding XPath, and `.text` is used to extract the text content.
team1 =  driver.find_element(By.XPATH, '//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[1]/div/div[2]/a/div/div/div[2]/div/div[1]/div[1]/p').text
team2 =  driver.find_element(By.XPATH, '//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[1]/div/div[2]/a/div/div/div[2]/div/div[2]/div[1]/p').text
results = driver.find_element(By.XPATH, '//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[1]/div/div[2]/a/div/div/p/span').text
loc =    driver.find_element(By.XPATH, '//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[1]/div/div[2]/a/div/div/div[1]/div/span/div').text.split('•')[1].split(',')[0]
dates = driver.find_element(By.XPATH, '//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[1]/div/div[1]').text

The `print` statement outputs the following message with obtained :`team1` `team2` `loc` `dates` `results`


print(f'The match between {team1} VS {team2} was held in {loc} |{dates}|  where {results}' )
Setting up a 10-second wait to ensure elements are present before interaction and iterating through indices 1 to 9, dynamically creating XPath expressions for each index:

- `team1_xpath`: Finds the first team's name.
- `team2_xpath`: Finds the second team's name.
- `result_xpath`: Finds the match result.
- `location_xpath`: Finds the match location (processed to extract the relevant part).
- `date_xpath`: Finds the match date.

For each index, it waits until the elements are present, extracts their text, and prints the details. If an error occurs, an error message is printed for the current index.
wait = WebDriverWait(driver, 10)                                                                                                # Wait for the element to appear (up to 10 seconds)


for i in range(1, 10):
    try:
        # Use f-string to dynamically insert the index in the XPath
        team1_xpath  = f'//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[{i}]/div/div[2]/a/div/div/div[2]/div/div[1]/div[1]/p'
        team2_xpath  = f'//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[{i}]/div/div[2]/a/div/div/div[2]/div/div[2]/div[1]/p'
        result_xpath = f'//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[{i}]/div/div[2]/a/div/div/p/span'
        location_xpath = f'//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[{i}]/div/div[2]/a/div/div/div[1]/div/span/div'
        date_xpath = f'//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[{i}]/div/div[1]'

        # Wait until the element is present
        team1_element  = wait.until(EC.presence_of_element_located((By.XPATH, team1_xpath)))
        team2_element  = wait.until(EC.presence_of_element_located((By.XPATH, team2_xpath)))
        result_element = wait.until(EC.presence_of_element_located((By.XPATH, result_xpath)))
        date_element   = wait.until(EC.presence_of_element_located((By.XPATH, date_xpath)))
        location_element = wait.until(EC.presence_of_element_located((By.XPATH, location_xpath)))
        location_element = location_element.text.split('•')[1].split(',')[0]

        print(f'{team1_element.text} | {team2_element.text} | {result_element.text} | {location_element} | {date_element.text} ')


    except Exception as e:
        print(f"Error occurred while processing index {i}: {e}")

Extracting match details from a webpage using a loop with dynamically generated XPath expressions. Key points include:

- **Data Collection**: Capturing team names, match results, locations, and dates.
- **Dynamic XPaths**: Using indexed XPaths to handle multiple matches.
- **Exception Handling**: Managing `TimeoutException` and general errors to stop the loop when needed.
- **Data Storage**: Organizing collected information into lists and creating a dictionary for further use.
wait = WebDriverWait(driver, 10)

team_1 = []
team_2 = []
winner = []
won_by = [] 
match_id = []
location = []
date = []


i = 1 

while True: 
    try:

        team1_xpath  = f'//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[{i}]/div/div[2]/a/div/div/div[2]/div/div[1]/div[1]/p'
        team2_xpath  = f'//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[{i}]/div/div[2]/a/div/div/div[2]/div/div[2]/div[1]/p'
        result_xpath = f'//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[{i}]/div/div[2]/a/div/div/p/span'
        location_xpath = f'//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[{i}]/div/div[2]/a/div/div/div[1]/div/span/div'
        date_xpath = f'//*[@id="main-container"]/div[5]/div/div[4]/div[1]/div[1]/div/div/div/div[{i}]/div/div[1]'

        team1_element  = wait.until(EC.presence_of_element_located((By.XPATH, team1_xpath)))
        team2_element  = wait.until(EC.presence_of_element_located((By.XPATH, team2_xpath)))
        result_element = wait.until(EC.presence_of_element_located((By.XPATH, result_xpath)))
        date_element   = wait.until(EC.presence_of_element_located((By.XPATH, date_xpath)))
        location_element = wait.until(EC.presence_of_element_located((By.XPATH, location_xpath)))
        location_element = location_element.text.split('•')[1].split(',')[0]

        split = result_element.text.split('won')
        winner_element = split[0]
        won_by_element = split[-1].split('by ')[-1]

        if date_element.text != '':
            date_temp = date_element.text


        team_1.append(team1_element.text)
        team_2.append(team2_element.text)
        winner.append(winner_element)
        won_by.append(won_by_element)
        match_id.append(i)
        date.append(date_temp)
        location.append(location_element)
        
        i += 1 

    except TimeoutException:
        print(f"Finished processing up to index {i-1}. No more elements found.")
        break 

    except Exception as e:
        print(f"Error occurred while processing index {i}: {e}")
        break  


# driver.quit()

dictionary = {'match_id': match_id, 'date': date , 'team_1': team_1, 'team_2': team_2, 'winner': winner, 'won_by': won_by, 'location' : location}
Converting the dictionary into a Pandas DataFrame named `df_matches`
df_matches = pd.DataFrame(dictionary)
displaying the last 15 rows of the DataFrame df_matches
df_matches[-15:]
Saving the DataFrame `df_matches` to a CSV file named `matches_dim.csv` without including row indices
df_matches.to_csv('matches_dim.csv', index=False)
#### Batting and bowling tables
The script is setting up a custom Chrome browser to scrape data from ESPN Cricinfo. It is opening the match schedule page, handling any overlays, and collecting scorecard links. It is then iterating through these links to extract detailed batting and bowling statistics for each match, including team names, player performances, and match outcomes. The collected data is being organized into dictionaries for both batting and bowling stats, preparing it for further analysis.

chrome_test_path ="D:\\Webscraping\\chrome-win64\\chrome-win64\\chrome.exe"                                                    # custom chrome path (the testing Chrome)

chromedriver_path = "D:\\Webscraping\\chromedriver-win64\\chromedriver.exe"                                                    # chromedriver Path

# Set ChromeOptions to use the specific Chrome binary
chrome_options = Options()
chrome_options.binary_location = chrome_test_path

# Initialize the ChromeDriver with the custom binary and service
service = Service(executable_path=chromedriver_path)
driver = webdriver.Chrome(service=service, options=chrome_options)

bat_match_id = []; bat_versus = []; batting_team = []; batsmen = []; out = []; runs_scored = []; balls = []; fours_scored = []; sixes_scored = []; sr = []; order = []
bowl_match_id = []; bowl_versus = []; bowling_team = []; bowler = []; overs = []; maidens = []; runs_given = []; wickets = []; economy = []; zeros = []; fours_given = []; sixes_given = []; wides = []; noballs = []



website = 'https://www.espncricinfo.com/series/icc-men-s-t20-world-cup-2024-1411166/match-schedule-fixtures-and-results'
driver.get(website)


# Handle overlays or pop-ups that may block clicks
try:
    close_button = WebDriverWait(driver, 25).until(
        EC.element_to_be_clickable((By.XPATH, '//*[@id="wzrk-cancel"]'))
    )
    close_button.click()  # Close the pop-up
except Exception as e:
    print(f"No overlay or pop-up to close: {e}")


# scorecard_links = driver.find_elements(By.XPATH, '//div[@class="ds-grow ds-px-4 ds-border-r ds-border-line-default-translucent"]')
full_table = driver.find_element(By.XPATH, '//div[@class="ds-w-full ds-bg-fill-content-prime ds-overflow-hidden ds-rounded-xl ds-border ds-border-line"]')
scorecard_details = full_table.find_elements(By.CLASS_NAME, "ds-no-tap-higlight")

scorecard_links = []
for link in scorecard_details:
    href_value = link.get_attribute('href')
    scorecard_links.append(href_value)



for id,i in enumerate(scorecard_links):
    driver.get(i)

    try:
        batting_tables = WebDriverWait(driver, 10).until( # Wait until all tables are present on the page (or until 10 seconds timeout)
        EC.presence_of_all_elements_located((By.XPATH, '//table[@class="ds-w-full ds-table ds-table-md ds-table-auto  ci-scorecard-table"]')))
        
        bowling_tables =  WebDriverWait(driver, 10).until( # Wait until all tables are present on the page (or until 10 seconds timeout)
        EC.presence_of_all_elements_located((By.XPATH, '//table[@class="ds-w-full ds-table ds-table-md ds-table-auto "]')))

        x_vs_y = driver.find_element(By.TAG_NAME, 'h1').text.split(',')[0]
        teams = driver.find_elements(By.XPATH, '//span[@class="ds-text-title-xs ds-font-bold ds-capitalize"]')
    except TimeoutException:
        continue


    # batting
    
    h= 0

    for batting_table in batting_tables:
        body = batting_table.find_element(By.TAG_NAME, 'tbody')
        multiple_tr = body.find_elements(By.CSS_SELECTOR, 'tr[class= ""]')[:-2]
        i = 1

        for tr in multiple_tr:
            bat_match_id.append(id+1)
            bat_versus.append(x_vs_y)
            batting_team.append(teams[h].text)
            order.append(i)
            batsmen.append(tr.find_element(By.XPATH, './td[1]').text)
            out.append(tr.find_element(By.XPATH, './td[2]').text)
            runs_scored.append(tr.find_element(By.XPATH, './td[3]').text)
            balls.append(tr.find_element(By.XPATH, './td[4]').text)    
            fours_scored.append(tr.find_element(By.XPATH, './td[6]').text)
            sixes_scored.append(tr.find_element(By.XPATH, './td[7]').text)
            sr.append(tr.find_element(By.XPATH, './td[8]').text)

            i += 1
        h = 1
    batting_dict = {'match_id':bat_match_id, 'versus':bat_versus, 'batting_team':batting_team, 'order':order, 'batsmen': batsmen, 'out':out ,'runs_scored':runs_scored, 'balls':balls, 'fours_scored':fours_scored, 'sixes_scored':sixes_scored, 'sr':sr}


    # bowling
    q = 1

    for bowling_table in bowling_tables:
        body = bowling_table.find_element(By.TAG_NAME, 'tbody')
        multiple_tr = body.find_elements(By.CSS_SELECTOR, 'tr[class= ""]')

        for tr in multiple_tr:
            bowl_match_id.append(id+1)
            bowl_versus.append(x_vs_y)
            try: 
                bowling_team.append(teams[q].text)
            except:
                bowling_team.append('NULL')
            bowler.append (tr.find_element(By.XPATH, './td[1]').text)
            overs.append  (tr.find_element(By.XPATH, './td[2]').text)
            maidens.append(tr.find_element(By.XPATH, './td[3]').text)
            runs_given.append   (tr.find_element(By.XPATH, './td[4]').text)
            wickets.append(tr.find_element(By.XPATH, './td[5]').text)
            economy.append(tr.find_element(By.XPATH, './td[6]').text)
            zeros.append  (tr.find_element(By.XPATH, './td[7]').text)
            fours_given.append  (tr.find_element(By.XPATH, './td[8]').text)
            sixes_given.append  (tr.find_element(By.XPATH, './td[9]').text)
            wides.append  (tr.find_element(By.XPATH, './td[10]').text)
            noballs.append(tr.find_element(By.XPATH, './td[11]').text)

        q = 0
    bowling_dict = {'match_id':bowl_match_id, 'versus':bowl_versus, 'bowling_team':bowling_team, 'bowler':bowler, 'overs':overs, 'maidens':maidens, 'runs_given': runs_given, 'wickets':wickets, 'economy':economy, 'zeros': zeros, 'fours_given': fours_given, 'sixes_given':sixes_given, 'wides':wides, 'noballs':noballs}    


# driver.quit()
Converting the dictionary into a Pandas DataFrame named `bowling_df` and diaplaying the DataFrame
bowling_df = pd.DataFrame(bowling_dict)
bowling_df
Converting the dictionary into a Pandas DataFrame named `batting_df` and diaplaying the DataFrame
batting_df = pd.DataFrame(batting_dict)
batting_df
Saving the DataFrame `bowling_df`, `batting_df` to a CSV files named `bowling_fact.csv` ,`batting_fact.csv` respectively without including row indices
bowling_df.to_csv('bowling_fact.csv', index=False)
batting_df.to_csv('batting_fact.csv', index=False)