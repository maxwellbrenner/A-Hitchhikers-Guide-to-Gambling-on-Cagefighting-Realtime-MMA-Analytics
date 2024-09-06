# A-Hitchhiker-s-Guide-to-Gambling-on-Cagefighting-Realtime-MMA-Analytics

# UFC Fight Scraper

### Function Descriptions (Ordered by Execution in Main)

### 1. `get_page_content(url)`
- **Description**: This function retrieves and parses the HTML content from the given URL using the `requests` library. It returns a BeautifulSoup object if the request is successful, otherwise, it prints an error message and returns `None`.

### 2. `extract_event_info(event_element)`
- **Description**: Extracts basic information for an event, including the event name, link, date, and location. Returns an event dictionary and the parsed content (soup) of the event page.

### 3. `extract_fight_info(fight_row)`
- **Description**: Extracts information about a specific fight from a table row, including fight link, winner, method of victory, and fighter details (names and links).

### 4. `get_fight_link_and_winner(fight_row)`
- **Description**: Extracts the fight link, winner, and method of victory based on the table row information. If no fight link is available, it defaults to `'N/A'`, and if no winner is determined, it defaults to `'N/A'`. It returns the fight link, winner, and method of victory.

### 5. `extract_fighter_info(fighter_row)`
- **Description**: Extracts basic information about two fighters (names and links) from a table row. It returns two dictionaries representing fighter A and fighter B, along with empty lists for rounds information.

### 6. `scrape_fight_info(fight_info)`
- **Description**: Scrapes detailed fight information from the fight page, including rounds and results. Updates the `fight_info` dictionary with this data, including weight class, title fight status, gender, and round-specific information for both fighters.

### 7. `extract_victory_and_round_data(fight_soup, fight_info)`
- **Description**: Extracts the method of victory, round of victory, time of victory, time format, and referee data from the fight page and updates the `fight_info` dictionary.

### 8. `extract_round_data(row_fighter_data, row_strikes_data, fighter_a_name, fighter_b_name, fight_info)`
- **Description**: Extracts round-by-round data for both fighters, including fighter stats and strike data, and updates the `fight_info` dictionary with this information.

### 9. `extract_fighter_data(row, index)`
- **Description**: Extracts detailed fight statistics for a fighter, such as knockdowns, significant strikes, total strikes, takedowns, submission attempts, reversals, and control time. It returns this data in a dictionary for a specified fighter (index 0 for fighter A, 1 for fighter B).

### 10. `extract_strikes_data(row, index)`
- **Description**: Extracts detailed strike data for a fighter from a table row. It includes head, body, leg, distance, clinch, and ground strikes for a specified fighter (index 0 for fighter A, 1 for fighter B). It returns a dictionary containing these details.

### 11. `scrape_fighter_info(fighter_info)`
- **Description**: Scrapes individual fighter details from the fighter’s page, including height, reach, and date of birth, and updates the `fighter_info` dictionary with these attributes.

### 12. `convert_to_inches(height_info)`
- **Description**: Converts a height string from the format "feet'inches"" into inches (e.g., "6'0"" to 72 inches). If the conversion fails, it returns `'N/A'` for both the original format and inches.

### 13. `convert_to_numerical_date(date_str)`
- **Description**: Converts a date string in the format `'%b %d, %Y'` (e.g., `'Jan 01, 2020'`) into a numerical date in the format `'%m-%d-%Y'`. Returns `'N/A'` if the conversion fails.

### 14. `parse_date(date_str)`
- **Description**: This function converts a date string into the format `'MM-DD-YYYY'`. If the input is `'N/A'`, it returns `'N/A'`. If the conversion fails, it catches the error and returns `'N/A'`.
  
### 15. `write_to_csv(events_list)`
- **Description**: Writes the collected event and fight data, including detailed round-by-round statistics, to a CSV file. Each row in the CSV represents a fight, and columns include event details, fighter statistics, and round information.


---

## Overview of the `main` Function

The `main` function orchestrates the web scraping process by following these steps:

1. **Fetch the Main Page**: It retrieves the main page that lists all completed UFC events using the `get_page_content` function.

2. **Parse Events**: It loops through the event elements (links to individual events) on the main page. For each event:
   - The `extract_event_info` function is called to gather the event's basic details (name, date, location) and to parse the event page.
   
3. **Parse Fights**: For each event, it retrieves the list of fights:
   - The `extract_fight_info` function extracts basic fight data such as fight link, winner, and fighter details from the event's fight table.
   - This information is added to the `fights` list in the event's dictionary.

4. **Scrape Detailed Fight and Fighter Information**: Once basic fight details are collected:
   - The `scrape_fight_info` function is called to scrape detailed fight data, such as round-by-round stats, methods of victory, and more.
   - The `scrape_fighter_info` function scrapes additional details for each fighter, including height, reach, and date of birth.

5. **Store Events**: The completed event (with detailed fight data) is added to the `events_list`.

6. **Write to CSV**: After processing all events and fights, the `write_to_csv` function is called to save the results into a CSV file.

7. **Error Handling**: The function includes basic error handling to ensure that if an error occurs, the data collected so far is still written to the CSV.

By the end of the `main` function, a detailed list of events and fight data is collected and written to a CSV file.


---

# UFCStats Web Scraper

This program scrapes data from the UFCStats website to extract detailed event, fight, and fighter statistics. It collects data such as fight results, significant strikes, fighter attributes, and outputs it into a CSV file for easy analysis.

## Starting Point of the Program

The program begins by executing the `main()` function. This function handles the overall flow, retrieving the main event page, scraping fight data for each event, and writing the output to a CSV file. Below is a step-by-step breakdown of the process:

### 1. Fetching the Main UFC Events Page

The program starts by requesting and parsing the UFCStats homepage for completed events. This is done by calling `get_page_content(url)`, where `url` points to the main UFC stats events page (`http://www.ufcstats.com/statistics/events/completed?page=all`).

![Homepage](image%20(UFCStats.com)/UFCStats%20-%20Homepage.png)
    
- The homepage contains a list of events, each represented by a hyperlink. The events are marked with their names, locations, and dates.
- Each event link is scraped and stored for further processing.

Code references:
- `get_page_content(url)`: Fetches the HTML page for completed UFC events.

```python
def get_page_content(url):
    """
    Retrieve and parse HTML content from a specified URL using a user-defined number of retries.
    Returns a BeautifulSoup object if successful, otherwise None.

    Parameters:
    - url (str): The URL from which to fetch content.

    Returns:
    - BeautifulSoup: Parsed HTML of the page, or None if the request fails after retries.
    """

    try:
        response = requests.get(url, headers=headers)
        if response.status_code == 200:
            return BeautifulSoup(response.content, 'html.parser')
        else:
            print(f"Failed to retrieve the page. Status code: {response.status_code}")
    except requests.RequestException as e:
        print(f"Request failed: {e}")

    print("All attempts failed. Returning None.")
    return None
```

- `extract_event_info(event_element)`: Extracts information about each event (name, link, date, and location).

### 2. Extracting Event Details

Once the main event page has been parsed, the program moves on to individual event pages, which list all the fights for that event.

![Event Page](image%20(UFCStats.com)/UFCStats%20-%20Event%20Page.png)
    
- Each event contains fight data for all matchups on that card.
- The `extract_fight_info(fight_row)` function is responsible for extracting each fight’s basic information, such as fighter names, links, and method of victory.
    
Code references:
- `extract_fight_info(fight_row)`: Extracts individual fight information like fighter names, method of victory, and fight link.

### 3. Scraping Fight-Specific Data

The program navigates to the fight-specific page for each individual fight in an event.

![Fight Page](image%20(UFCStats.com)/UFCStats%20-%20Fight%20Page.png)
    
- On this page, detailed information about the fight is available, such as the weight class, fight result (method of victory), referee, and round-specific statistics (significant strikes, knockdowns, etc.).
- The `scrape_fight_info(fight_info)` function handles gathering these details from the fight page and updates the `fight_info` dictionary.

Code references:
- `scrape_fight_info(fight_info)`: Scrapes detailed fight data, including rounds, weight class, and method of victory.

### 4. Extracting Round-by-Round Data

For each fight, detailed round-by-round statistics are extracted, including the number of significant strikes, body strikes, leg strikes, and more. This is handled by two helper functions:
    
![Round Statistics (General)](image%20(UFCStats.com)/UFCStats%20-%20Round%20Statistics%20(General).png)
- **Significant Strikes:** The `extract_strikes_data()` function collects data about strikes such as strikes to the head, body, legs, and distance strikes.

![Round Statistics (Strikes)](image%20(UFCStats.com)/UFCStats%20-%20Round%20Statistics%20(Strikes).png)
- **General Fight Data:** The `extract_fighter_data()` function extracts other round-based data, such as knockdowns, submission attempts, takedowns, and control time.
    
Code references:
- `extract_strikes_data(row, index)`: Extracts round-based strike information.
- `extract_fighter_data(row, index)`: Extracts fighter-specific statistics (e.g., knockdowns, takedowns, submission attempts) for each round.

### 5. Scraping Fighter Information

The program also scrapes individual fighter pages to gather more specific details such as height, reach, and date of birth. This is useful for contextualizing the statistics.
    
![Fighter Info](image%20(UFCStats.com)/UFCStats%20-%20Fighter%20Info.png)
    
- The `scrape_fighter_info(fighter_info)` function handles this task, retrieving attributes like height and reach from the fighter's profile page.
    
Code references:
- `scrape_fighter_info(fighter_info)`: Gathers personal statistics for each fighter, such as height and reach.

### 6. Writing the Data to CSV

Once all event, fight, and fighter data has been collected, the program writes the results into a CSV file using the `write_to_csv(events_list)` function.

- The headers of the CSV include event name, event date, location, winner, fighter statistics, and round-by-round details.
- For each fight, it includes a row with detailed information about the event, fighter, and round statistics.

Code references:
- `write_to_csv(events_list)`: Writes all collected fight and fighter data to a CSV file for analysis.

