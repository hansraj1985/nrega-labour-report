import requests
import pandas as pd
from bs4 import BeautifulSoup
from datetime import datetime

# NREGA URL
url = "https://nreganarep.nic.in/netnrega/dpc_sms_new.aspx?lflag=eng&page=d&Short_Name=CH&state_name=CHHATTISGARH&state_code=33&district_name=KAWARDHA&district_code=3302&fin_year=2024-2025&dt=&EDepartment=ALL&wrkcat=ALL&worktype=ALL&Digest=NoA4+WEZog7x0JbEzP89vw"

# Fetch page content
response = requests.get(url)
response.encoding = "utf-8"
soup = BeautifulSoup(response.text, "html.parser")

# Find all tables
tables = soup.find_all("table")

if not tables:
    print("⚠️ No tables found on the page.")
else:
    # Assume first large table is the report
    table = tables[0]

    # Extract headers
    headers = [th.get_text(strip=True) for th in table.find_all("th")]

    # Extract rows
    rows = []
    for tr in table.find_all("tr")[1:]:
        cells = [td.get_text(strip=True) for td in tr.find_all(["td", "th"])]
        if cells:
            rows.append(cells)

    # Create DataFrame
    df = pd.DataFrame(rows, columns=headers if headers else None)

    # Save Excel file with current date
    date_str = datetime.now().strftime("%Y-%m-%d")
    file_name = f"labour_report_{date_str}.xlsx"
    df.to_excel(file_name, index=False)
    print(f"✅ Excel file saved: {file_name}")
