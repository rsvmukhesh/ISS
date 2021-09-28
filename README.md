# ISS

## Description
The ISS(**International Space Station**) project is done Using **Python** with **Smptlib**, **Requests** modules

## Code 

import requests
from datetime import datetime
import smtplib
import time

MY_EMAIL = "chinnavicky69@gmail.com"
PSWD = "mukhesh#@#"

MY_LAT = 14.467354# Your latitude
MY_LONG = 78.824135 # Your longitude

def iss_overhead():
    response = requests.get(url="http://api.open-notify.org/iss-now.json")
    response.raise_for_status()
    data = response.json()

    iss_latitude = float(data["iss_position"]["latitude"])
    iss_longitude = float(data["iss_position"]["longitude"])

    # Your position is within +5 or -5 degrees of the ISS position.
    if MY_LAT - iss_latitude == 5 or MY_LAT - iss_latitude == -5:
        if MY_LONG - iss_longitude ==5 or MY_LONG - iss_longitude == -5:
            return True
    else:
        return False


def is_night():
    parameters = {
        "lat": MY_LAT,
        "lng": MY_LONG,
        "formatted": 0,
    }

    response = requests.get("https://api.sunrise-sunset.org/json", params=parameters)
    response.raise_for_status()
    data = response.json()
    sunrise = int(data["results"]["sunrise"].split("T")[1].split(":")[0])
    sunset = int(data["results"]["sunset"].split("T")[1].split(":")[0])

    time_now = datetime.now().hour

    if time_now >= sunset or time_now <= sunrise:
        return True

while True:
    time.sleep(60)
    if iss_overhead() and is_night():
        connection = smtplib.SMTP("smtp.gmail.com")
        connection.starttls()
        connection.login(user=MY_EMAIL, password=PSWD)
        connection.sendmail(from_addr=MY_EMAIL,
                            to_addrs=MY_EMAIL,
                            msg="subject:Lokk Up\n\n The ISS above you in Sky"
        )


