# A-Hitchhiker-s-Guide-to-Gambling-on-Cagefighting-Realtime-MMA-Analytics

# UFC Fight Scraper

## List of All Functions with Descriptions

### 1. `get_page_content(url)`
- **Description**: This function retrieves and parses the HTML content from the given URL using the `requests` library. It returns a BeautifulSoup object if the request is successful, otherwise, it prints an error message and returns `None`.

### 2. `parse_date(date_str)`
- **Description**: This function converts a date string into the format `'MM-DD-YYYY'`. If the input is `'N/A'`, it returns `'N/A'`. If the conversion fails, it catches the error and returns `'N/A'`.

### 3. `extract_fighter_info(fighter_row)`
- **Description**: Extracts basic information about two fighters (names and links) from a table row. It returns two dictionaries representing fighter A and fighter B, along with empty lists for rounds information.

### 4. `convert_to_inches(height_info)`
- **Description**: Converts a height string from the format "feet'inches"" into inches (e.g., "6'0"" to 72 inches). If the conversion fails, it returns `'N/A'` for both the original format and inches.

### 5. `convert_to_numerical_date(date_str)`
- **Description**: Converts a date string in the format `'%b %d, %Y'` (e.g., `'Jan 01, 2020'`) into a numerical date in the format `'%m-%d-%Y'`. Returns `'N/A'` if the conversion fails.

### 6. `extract_strikes_data(row, index)`
- **Description**: Extracts detailed strike data for a fighter from a table row. It includes head, body, leg, distance, clinch, and ground strikes for a specified fighter (index 0 for fighter A, 1 for fighter B). It returns a dictionary containing these details.

### 7. `extract_fighter_data(row, index)`
- **Description**: Extracts detailed fight statistics for a fighter, such as knockdowns, significant strikes, total strikes, takedowns, submission attempts, reversals, and control time. It returns this data in a dictionary for a specified fighter (index 0 for fighter A, 1 for fighter B).

### 8. `get_fight_link_and_winner(fight_row)`
- **Description**: Extracts the fight link, winner, and method of victory based on the table row information. If no fight link is available, it defaults to `'N/A'`, and if no winner is determined, it defaults to `'N/A'`. It returns the fight link, winner, and method of victory.

### 9. `extract_round_data(row_fighter_data, row_strikes_data, fighter_a_name, fighter_b_name, fight_info)`
- **Description**: Extracts round-by-round data for both fighters, including fighter stats and strike data, and updates the `fight_info` dictionary with this information.

### 10. `extract_victory_and_round_data(fight_soup, fight_info)`
- **Description**: Extracts the method of victory, round of victory, time of victory, time format, and referee data from the fight page and updates the `fight_info` dictionary.

### 11. `scrape_fight_info(fight_info)`
- **Description**: Scrapes detailed fight information from the fight page, including rounds and results. Updates the `fight_info` dictionary with this data, including weight class, title fight status, gender, and round-specific information for both fighters.

### 12. `scrape_fighter_info(fighter_info)`
- **Description**: Scrapes individual fighter details from the fighter’s page, including height, reach, and date of birth, and updates the `fighter_info` dictionary with these attributes.

### 13. `write_to_csv(events_list)`
- **Description**: Writes the collected event and fight data, including detailed round-by-round statistics, to a CSV file. Each row in the CSV represents a fight, and columns include event details, fighter statistics, and round information.

### 14. `extract_event_info(event_element)`
- **Description**: Extracts basic information for an event, including the event name, link, date, and location. Returns an event dictionary and the parsed content (soup) of the event page.

### 15. `extract_fight_info(fight_row)`
- **Description**: Extracts information about a specific fight from a table row, including fight link, winner, method of victory, and fighter details (names and links).

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
