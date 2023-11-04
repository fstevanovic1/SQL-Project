Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

SELECT "city", SUM("transactionRevenue") FROM all_sessions
GROUP BY "city"
HAVING SUM("transactionRevenue") IS NOT NULL
ORDER BY SUM("transactionRevenue") DESC

SELECT "country", SUM("transactionRevenue") FROM all_sessions
GROUP BY "country"
HAVING SUM("transactionRevenue") IS NOT NULL
ORDER BY SUM("transactionRevenue") DESC


Answer: The cities with the largest transaction revenues are Sunnyvale, Chicago and Mountain View.
The countries with the largest transaction revenues are the US, Sweden and Mexico. 
I did not include the transaction revenue of those transactions that had no city set.




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

SELECT "country", AVG("units_sold") FROM all_sessions
JOIN analytics USING ("fullVisitorId")
WHERE units_sold > 0
GROUP BY "country"
ORDER BY AVG("units_sold") DESC

SELECT "city", AVG("units_sold") FROM all_sessions
JOIN analytics USING ("fullVisitorId")
WHERE units_sold > 0
GROUP BY "city"
ORDER BY AVG("units_sold") DESC

Answer:

Average Number of products ordered from visitors in each country

"United States"	19.2449215143120960
"Czechia"	15.1818181818181818
"Mexico"	1.8333333333333333
"Canada"	1.5934065934065934
"Bulgaria"	1.5000000000000000
"Germany"	1.2500000000000000
"Hong Kong"	1.2321428571428571
"Japan"	1.1351351351351351
"India"	1.1111111111111111
"Finland"	1.00000000000000000000
"France"	1.00000000000000000000
"Guatemala"	1.00000000000000000000
"Indonesia"	1.00000000000000000000
"Ireland"	1.00000000000000000000
"Israel"	1.00000000000000000000
"Maldives"	1.00000000000000000000
"Netherlands"	1.00000000000000000000
"Norway"	1.00000000000000000000
"Panama"	1.00000000000000000000
"Portugal"	1.00000000000000000000
"Romania"	1.00000000000000000000
"Russia"	1.00000000000000000000
"Singapore"	1.00000000000000000000
"South Korea"	1.00000000000000000000
"Spain"	1.00000000000000000000
"Sri Lanka"	1.00000000000000000000
"Sweden"	1.00000000000000000000
"Switzerland"	1.00000000000000000000
"Taiwan"	1.00000000000000000000
"Thailand"	1.00000000000000000000
"United Kingdom"	1.00000000000000000000
"Uruguay"	1.00000000000000000000
"Australia"	1.00000000000000000000
"Vietnam"	1.00000000000000000000
"Austria"	1.00000000000000000000
"Belarus"	1.00000000000000000000
"Belgium"	1.00000000000000000000
"Chile"	1.00000000000000000000
"Colombia"	1.00000000000000000000
"Denmark"	1.00000000000000000000
"Dominican Republic"	1.00000000000000000000
"Egypt"	1.00000000000000000000

Average Number of products ordered from visitors in each city

"San Bruno"	52.6666666666666667
"not available in demo dataset"	26.8972656250000000
"Mountain View"	16.1651376146788991
"San Jose"	8.5652173913043478
"Salem"	7.5454545454545455
"New York"	6.8953488372093023
"Chicago"	6.1941176470588235
"Nashville"	5.3333333333333333
"Jersey City"	5.2727272727272727
"Charlotte"	4.6467065868263473
"Sunnyvale"	4.0688073394495413





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

SELECT "country", "v2ProductCategory", count("v2ProductCategory") FROM all_sessions
GROUP BY "country", "v2ProductCategory"
ORDER BY "country", count("v2ProductCategory") DESC

SELECT "city", "v2ProductCategory", count("v2ProductCategory") FROM all_sessions
GROUP BY "city", "v2ProductCategory"
ORDER BY "city", count("v2ProductCategory") DESC


Answer: Generally the largest amount of products bought tend to be from youtube for each country. 
	If we look at each city though there is a large amount of Men's T-shirts being bought alongside merchandise from youtube.





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

WITH cte AS 
	(SELECT "country", "v2ProductName", count("v2ProductName") AS amountOfProduct FROM all_sessions
	GROUP BY "country", "v2ProductName"
	ORDER BY "country", count("v2ProductName") DESC)
	

SELECT "country", "v2ProductName", amountOfProduct FROM cte
WHERE amountOfProduct = (SELECT maxAmount FROM (
							SELECT "country", MAX(amountOfProduct) AS maxAmount 
							FROM cte 
							GROUP BY "country"
							ORDER BY "country") AS cm 
						 	WHERE cte."country" = cm."country")

WITH cte AS 
	(SELECT "city", "v2ProductName", count("v2ProductName") AS amountOfProduct FROM all_sessions
	GROUP BY "city", "v2ProductName"
	ORDER BY "city", count("v2ProductName") DESC)
	

SELECT "city", "v2ProductName", amountOfProduct FROM cte
WHERE amountOfProduct = (SELECT maxAmount FROM (
							SELECT "city", MAX(amountOfProduct) AS maxAmount 
							FROM cte 
							GROUP BY "city"
							ORDER BY "city") AS cm 
						 	WHERE cte."city" = cm."city")


Answer: The above code grabs the maximum amount including ties so we can also compare
to see if there is any meaning between multiple products. Generally speaking Youtube
seems to be one of the most popular brands on a country by country basis although there
are exceptions such as the US which likes the Google Men's Shirt the most.

The city table is harder to parse as there are significantly more cities with a larger
spread of picks on what is popular. We can see though that the YouTube Bottle infuser
is quite popular with people whose city is not available. Also two american cities in
New York and San Francisco both have the Google Men's Shirt the most which is in line
with the rest of the country. On the other hand Mountain View has the Nest® Cam 
Indoor Security Camera as its most popular item(which also the most popular item within
any city excluding the people without one).


Most Popular items by country

"(not set)"	"Google Men's Long Sleeve Raglan Ocean Blue"	2
"(not set)"	"YouTube Men's Long Sleeve Pullover Badge Tee Heather"	2
"Albania"	"Google Women's Lightweight Microfleece Jacket"	1
"Albania"	"YouTube Men's Vintage Tank"	1
"Albania"	"22 oz YouTube Bottle Infuser"	1
"Algeria"	"Metal Earbuds with Small Zipper Case"	1
"Algeria"	"YouTube Notebook and Pen Set"	1
"Algeria"	"Google Toddler Raglan Shirt Blue Heather/Navy"	1
"Algeria"	"YouTube Twill Cap"	1
"Argentina"	"Google Alpine Style Backpack"	3
"Argentina"	"YouTube Wool Heather Cap Heather/Black"	3
"Armenia"	"Windup Android"	1
"Australia"	"22 oz YouTube Bottle Infuser"	8
"Australia"	"YouTube Twill Cap"	8
"Austria"	"YouTube Men's Short Sleeve Hero Tee White"	2
"Austria"	"YouTube Wool Heather Cap Heather/Black"	2
"Austria"	"YouTube RFID Journal"	2
"Austria"	"YouTube Onesie Heather"	2
"Bahamas"	"YouTube Men's Vintage Henley"	1
"Bahamas"	"Google Twill Cap"	1
"Bahamas"	"Android Journal Book Set"	1
"Bahrain"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Bahrain"	"YouTube Men's 3/4 Sleeve Henley"	1
"Bahrain"	"Google Accent Insulated Stainless Steel Bottle"	1
"Bahrain"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Bangladesh"	"Electronics Accessory Pouch"	2
"Bangladesh"	"YouTube RFID Journal"	2
"Bangladesh"	"Google Men's Vintage Badge Tee White"	2
"Bangladesh"	"YouTube Men's Vintage Henley"	2
"Barbados"	"Google Men's Pullover Hoodie Grey"	1
"Barbados"	"Compact Bluetooth Speaker"	1
"Belarus"	"Reusable Shopping Bag"	1
"Belarus"	"Google Women's Short Sleeve Hero Tee Sky Blue"	1
"Belarus"	"YouTube Men's Long Sleeve Pullover Badge Tee Heather"	1
"Belarus"	"Google Women's Performance Full Zip Jacket Black"	1
"Belarus"	"Google Stylus Pen w/ LED Light"	1
"Belarus"	"Android Men's Short Sleeve Hero Tee Heather"	1
"Belgium"	"YouTube Trucker Hat"	3
"Belgium"	"YouTube Leatherette Notebook Combo"	3
"Belgium"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	3
"Belize"	"Android Glass Water Bottle with Black Sleeve"	1
"Bolivia"	"Android Men's Short Sleeve Hero Tee Heather"	1
"Bolivia"	"Google G Noise-reducing Bluetooth Headphones"	1
"Bolivia"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Bolivia"	"Aria Bluetooth Speaker"	1
"Bosnia & Herzegovina"	"You Tube Toddler Short Sleeve Tee Red"	1
"Bosnia & Herzegovina"	"24 oz YouTube Sergeant Stripe Bottle"	1
"Bosnia & Herzegovina"	"YouTube Leatherette Notebook Combo"	1
"Botswana"	"Waterproof Backpack"	1
"Brazil"	"Google Men's Vintage Badge Tee Black"	6
"Brunei"	"YouTube Men's Vintage Henley"	1
"Brunei"	"You Tube Toddler Short Sleeve Tee Red"	1
"Bulgaria"	"Google Women's Short Sleeve Badge Tee Red Heather"	2
"Cambodia"	"Google Lunch Bag"	1
"Cambodia"	"YouTube Twill Cap"	1
"Cambodia"	"Google Men's Short Sleeve Performance Badge Tee Navy"	1
"Cambodia"	"Micro Wireless Earbud"	1
"Cambodia"	"Google Men's Long Sleeve Pullover Badge Tee Heather"	1
"Cambodia"	"Women's YouTube Short Sleeve Hero Tee Black"	1
"Canada"	"22 oz YouTube Bottle Infuser"	13
"Canada"	"YouTube Twill Cap"	13
"Chile"	"24 oz YouTube Sergeant Stripe Bottle"	2
"China"	"Google Men's Vintage Badge Tee White"	2
"Colombia"	"22 oz YouTube Bottle Infuser"	4
"Costa Rica"	"Plastic Sliding Flashlight"	1
"Costa Rica"	"Google Women's Short Sleeve Hero Tee White"	1
"Costa Rica"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	1
"Costa Rica"	"Google Accent Insulated Stainless Steel Bottle"	1
"Costa Rica"	"Google Power Bank"	1
"Costa Rica"	"Pen Pencil & Highlighter Set"	1
"Côte d’Ivoire"	"YouTube Custom Decals"	2
"Croatia"	"Google Men's Watershed Full Zip Hoodie Grey"	2
"Croatia"	"24 oz YouTube Sergeant Stripe Bottle"	2
"Croatia"	"YouTube Men's Vintage Henley"	2
"Cyprus"	"Google Men's Performance Polo Grey/Black"	1
"Cyprus"	"Google Men's Short Sleeve Hero Tee Light Blue"	1
"Cyprus"	"Suitcase Organizer Cubes"	1
"Cyprus"	"Google Men's Airflow 1/4 Zip Pullover Lapis"	1
"Cyprus"	"YouTube Wool Heather Cap Heather/Black"	1
"Cyprus"	"Google Men's Microfiber 1/4 Zip Pullover Grey/Black"	1
"Czechia"	"YouTube Hard Cover Journal"	6
"Denmark"	"YouTube Hard Cover Journal"	5
"Dominican Republic"	"Google Men's Vintage Tank"	1
"Dominican Republic"	"YouTube Twill Cap"	1
"Dominican Republic"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Dominican Republic"	"Google Women's Lightweight Microfleece Jacket"	1
"Dominican Republic"	"YouTube Custom Decals"	1
"Dominican Republic"	"YouTube Leatherette Notebook Combo"	1
"Dominican Republic"	"Bottle Opener Clip"	1
"Dominican Republic"	"Google Trucker Hat"	1
"Dominican Republic"	"22 oz YouTube Bottle Infuser"	1
"Dominican Republic"	"Google Men's Vintage Badge Tee Black"	1
"Dominican Republic"	"Electronics Accessory Pouch"	1
"Dominican Republic"	"Insulated Bottle"	1
"Ecuador"	"Google Men's Short Sleeve Badge Tee Charcoal"	1
"Ecuador"	"Metal Earbuds with Small Zipper Case"	1
"Ecuador"	"Google Men's  Zip Hoodie"	1
"Ecuador"	"Google Tube Power Bank"	1
"Ecuador"	"Android Men's Long Sleeve Badge Crew Tee Heather"	1
"Ecuador"	"Google Women's Short Sleeve Hero Tee Sky Blue"	1
"Ecuador"	"YouTube Onesie Heather"	1
"Ecuador"	"YouTube Men's Vintage Tee"	1
"Ecuador"	"Android Men's Vintage Tee"	1
"Ecuador"	"Google Women's Short Sleeve Badge Tee Red Heather"	1
"Ecuador"	"YouTube RFID Journal"	1
"Ecuador"	"Google Men's Watershed Full Zip Hoodie Grey"	1
"Ecuador"	"Android Men's  Zip Hoodie"	1
"Ecuador"	"Google Men's Skater Tee Grey"	1
"Egypt"	"Google Men's Performance Polo Grey/Black"	1
"Egypt"	"Google Men's Vintage Badge Tee Sage"	1
"Egypt"	"22 oz YouTube Bottle Infuser"	1
"Egypt"	"Android Men's Short Sleeve Hero Tee White"	1
"Egypt"	"Android RFID Journal"	1
"Egypt"	"Google Men's Vintage Badge Tee Black"	1
"Egypt"	"Google Men's Long Sleeve Raglan Ocean Blue"	1
"Egypt"	"You Tube Toddler Short Sleeve Tee Red"	1
"Egypt"	"YouTube Men's Long & Lean Tee Charcoal"	1
"Egypt"	"YouTube Notebook Set"	1
"Egypt"	"YouTube Men's Vintage Henley"	1
"Egypt"	"Google Men's Long Sleeve Pullover Badge Tee Heather"	1
"Egypt"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"El Salvador"	"Google Men's Quilted Insulated Vest Battleship Grey"	1
"El Salvador"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"El Salvador"	"Google Trucker Hat"	1
"El Salvador"	"Google  Women's Muscle Tee"	1
"El Salvador"	"YouTube Notebook and Pen Set"	1
"El Salvador"	"Google Women's Short Sleeve V-Neck Tee Lavender"	1
"El Salvador"	"Google Device Holder Sticky Pad"	1
"El Salvador"	"Android Men's  Zip Hoodie"	1
"El Salvador"	"Retractable Ballpoint Pen Red"	1
"El Salvador"	"Google Wool Heather Cap Heather/Navy"	1
"Estonia"	"YouTube Spiral Journal with Pen"	1
"Estonia"	"Waze Mood Original Window Decal"	1
"Estonia"	"Google Men's Quilted Insulated Vest Black"	1
"Estonia"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Estonia"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	1
"Estonia"	"Google Zipper-front Sports Bag"	1
"Estonia"	"Android Men's Vintage Tee"	1
"Estonia"	"Electronics Accessory Pouch"	1
"Estonia"	"Google Insulated Stainless Steel Bottle"	1
"Estonia"	"Google Car Clip Phone Holder"	1
"Estonia"	"22 oz Android Bottle"	1
"Estonia"	"Android Men's  Zip Hoodie"	1
"Ethiopia"	"YouTube Hard Cover Journal"	1
"Ethiopia"	"YouTube Leatherette Notebook Combo"	1
"Finland"	"Android Onesie Gold"	1
"Finland"	"Google Accent Insulated Stainless Steel Bottle"	1
"Finland"	"Google Laptop and Cell Phone Stickers"	1
"Finland"	"YouTube Men's Short Sleeve Hero Tee White"	1
"Finland"	"Recycled Mouse Pad"	1
"Finland"	"Google Device Holder Sticky Pad"	1
"Finland"	"Keyboard DOT Sticker"	1
"Finland"	"Google Tube Power Bank"	1
"Finland"	"Google Twill Cap"	1
"Finland"	"Digital Lightshow Smart Speaker and Notification Center"	1
"Finland"	"Google Vintage Henley Grey/Black"	1
"Finland"	"Google Infant Zip Hood Royal Blue"	1
"Finland"	"Rocket Flashlight"	1
"Finland"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	1
"Finland"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"France"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	8
"French Polynesia"	"YouTube Notebook and Pen Set"	1
"Georgia"	"YouTube Custom Decals"	3
"Germany"	"YouTube Twill Cap"	13
"Ghana"	"YouTube Luggage Tag"	1
"Ghana"	"YouTube Men's Vintage Tank"	1
"Ghana"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Gibraltar"	"YouTube Men's Eco-Jersey Henley"	1
"Greece"	"YouTube Trucker Hat"	4
"Guatemala"	"Google Blackout Cap"	1
"Guatemala"	"Android Men's Long Sleeve Badge Crew Tee Heather"	1
"Guatemala"	"YouTube Twill Cap"	1
"Guatemala"	"Google Doodle Decal"	1
"Guatemala"	"Android Wool Heather Cap Heather/Black"	1
"Guatemala"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Guatemala"	"Google Hard Cover Journal"	1
"Guatemala"	"Google Men's Lightweight Microfleece Jacket Black"	1
"Guatemala"	"Google Twill Cap"	1
"Haiti"	"Google Men's Short Sleeve Hero Tee Heather"	1
"Honduras"	"Google Women's Lightweight Microfleece Jacket"	1
"Honduras"	"YouTube Wool Heather Cap Heather/Black"	1
"Honduras"	"YouTube Notebook and Pen Set"	1
"Honduras"	"Nest® Cam Outdoor Security Camera - USA"	1
"Honduras"	"24 oz YouTube Sergeant Stripe Bottle"	1
"Hong Kong"	"Galaxy Screen Cleaning Cloth"	2
"Hong Kong"	"Google Men's Short Sleeve Hero Tee Light Blue"	2
"Hong Kong"	"Recycled Mouse Pad"	2
"Hong Kong"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	2
"Hong Kong"	"Leatherette Journal"	2
"Hong Kong"	"YouTube Men's Short Sleeve Hero Tee Black"	2
"Hong Kong"	"Sport Bag"	2
"Hungary"	"YouTube Wool Heather Cap Heather/Black"	2
"Hungary"	"YouTube Notebook and Pen Set"	2
"Hungary"	"YouTube Men's Short Sleeve Hero Tee Black"	2
"Hungary"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	2
"Iceland"	"Gel Roller Pen"	1
"Iceland"	"Google Insulated Stainless Steel Bottle"	1
"Iceland"	"Google Bluetooth Speaker-Power Bank"	1
"India"	"YouTube Custom Decals"	22
"Indonesia"	"22 oz YouTube Bottle Infuser"	6
"Iraq"	"Android Men's  Zip Hoodie"	1
"Iraq"	"Google Men's Vintage Badge Tee Black"	1
"Iraq"	"Android 5-Panel Low Cap"	1
"Iraq"	"YouTube Notebook and Pen Set"	1
"Ireland"	"24 oz YouTube Sergeant Stripe Bottle"	4
"Israel"	"22 oz YouTube Bottle Infuser"	3
"Israel"	"Nest® Protect Smoke + CO Black Wired Alarm-USA"	3
"Italy"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	5
"Jamaica"	"Google Men's  Zip Hoodie"	1
"Japan"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	7
"Jersey"	"Google Tri-blend Hoodie Grey"	1
"Jordan"	"Softsided Travel Pouch Set"	1
"Jordan"	"Set of 3 Packing Cubes"	1
"Jordan"	"Metal Earbuds with Small Zipper Case"	1
"Kazakhstan"	"Collapsible Shopping Bag"	1
"Kazakhstan"	"YouTube Men's Short Sleeve Hero Tee White"	1
"Kazakhstan"	"Android Men's Vintage Tee"	1
"Kenya"	"Google Flashlight"	1
"Kenya"	"Google Men's Skater Tee Grey"	1
"Kenya"	"Google Women's Short Sleeve Hero Tee Grey"	1
"Kenya"	"Google Men's Quilted Insulated Vest Battleship Grey"	1
"Kosovo"	"Google Men's Vintage Badge Tee Black"	1
"Kuwait"	"Google 17oz Stainless Steel Sport Bottle"	1
"Kuwait"	"Google Men's Vintage Badge Tee Green"	1
"Kuwait"	"Women's YouTube Short Sleeve Hero Tee Black"	1
"Kuwait"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Kuwait"	"Google Women's Fleece Hoodie"	1
"Kuwait"	"Waterproof Backpack"	1
"Kyrgyzstan"	"Rocket Flashlight"	1
"Laos"	"YouTube Hard Cover Journal"	2
"Latvia"	"UpCycled Handlebar Bag"	1
"Latvia"	"YouTube RFID Journal"	1
"Latvia"	"Google Men's Microfiber 1/4 Zip Pullover Blue/Indigo"	1
"Latvia"	"Google Women's Hero V-Neck Tee White"	1
"Latvia"	"Google Lunch Bag"	1
"Latvia"	"Red Spiral Google Notebook"	1
"Latvia"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	1
"Latvia"	"YouTube Twill Cap"	1
"Lebanon"	"YouTube RFID Journal"	1
"Lithuania"	"YouTube Luggage Tag"	2
"Luxembourg"	"Google Toddler Raglan Shirt Blue Heather/Navy"	1
"Macau"	"YouTube Leatherette Notebook Combo"	1
"Macau"	"Google Twill Cap"	1
"Macau"	"Google Men's 100% Cotton Short Sleeve Hero Tee Red"	1
"Macedonia (FYROM)"	"Color Changing Grip Pen"	1
"Macedonia (FYROM)"	"22 oz YouTube Bottle Infuser"	1
"Macedonia (FYROM)"	"Google Women's Performance Full Zip Jacket Black"	1
"Macedonia (FYROM)"	"Google Men's 100% Cotton Short Sleeve Hero Tee Red"	1
"Malaysia"	"Google Men's Short Sleeve Hero Tee Heather"	3
"Maldives"	"Google Men's Performance Full Zip Jacket Black"	1
"Mali"	"YouTube Custom Decals"	1
"Malta"	"YouTube Women's Fleece Hoodie Black"	1
"Malta"	"YouTube RFID Journal"	1
"Martinique"	"Google Water Resistant Bluetooth Speaker"	1
"Martinique"	"YouTube Men's Fleece Hoodie Black"	1
"Mauritius"	"Google Power Bank"	1
"Mauritius"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	1
"Mexico"	"YouTube Men's Short Sleeve Hero Tee Black"	5
"Moldova"	"24 oz YouTube Sergeant Stripe Bottle"	1
"Moldova"	"YouTube Custom Decals"	1
"Montenegro"	"YouTube Custom Decals"	1
"Morocco"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	1
"Morocco"	"YouTube Custom Decals"	1
"Morocco"	"Google Women's Short Sleeve Hero Tee Black"	1
"Morocco"	"22 oz YouTube Bottle Infuser"	1
"Morocco"	"YouTube Wool Heather Cap Heather/Black"	1
"Morocco"	"Google Youth Tee Fruit Games Pineapple"	1
"Morocco"	"YouTube Notebook and Pen Set"	1
"Morocco"	"Google Stylus Pen w/ LED Light"	1
"Morocco"	"Google Tote Bag"	1
"Morocco"	"Google 3/4 Sleeve Raglan Badge Henley Black"	1
"Myanmar (Burma)"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"Myanmar (Burma)"	"22 oz YouTube Bottle Infuser"	1
"Myanmar (Burma)"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Nepal"	"22 oz YouTube Bottle Infuser"	1
"Nepal"	"Google Rucksack"	1
"Nepal"	"YouTube Women's Racer Back Tank Black"	1
"Nepal"	"Google Women's Short Sleeve Shirt Blue"	1
"Netherlands"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	5
"Netherlands"	"22 oz YouTube Bottle Infuser"	5
"Netherlands"	"YouTube Men's Vintage Henley"	5
"Netherlands"	"YouTube Twill Cap"	5
"New Zealand"	"YouTube RFID Journal"	3
"New Zealand"	"22 oz YouTube Bottle Infuser"	3
"Nicaragua"	"Compact Selfie Stick"	1
"Nicaragua"	"Gel Roller Pen"	1
"Nigeria"	"Google Alpine Style Backpack"	1
"Nigeria"	"Google Twill Cap"	1
"Nigeria"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Nigeria"	"YouTube Spiral Journal with Pen"	1
"Nigeria"	"YouTube Onesie Heather"	1
"Nigeria"	"Keyboard DOT Sticker"	1
"Nigeria"	"YouTube Men's Vintage Tank"	1
"Nigeria"	"Google Lunch Bag"	1
"Norway"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	2
"Norway"	"Google Laptop and Cell Phone Stickers"	2
"Norway"	"YouTube Men's 3/4 Sleeve Henley"	2
"Oman"	"16 oz. Hot/Cold Tumbler"	1
"Oman"	"YouTube Hard Cover Journal"	1
"Pakistan"	"22 oz YouTube Bottle Infuser"	4
"Palestine"	"Google Laptop Backpack"	1
"Panama"	"Google Men's Short Sleeve Hero Tee Heather"	1
"Panama"	"Suitcase Organizer Cubes"	1
"Panama"	"Android Lunch Kit"	1
"Panama"	"Women's YouTube Short Sleeve Hero Tee Black"	1
"Panama"	"Google RFID Journal"	1
"Panama"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Panama"	"YouTube Custom Decals"	1
"Panama"	"22 oz YouTube Bottle Infuser"	1
"Panama"	"Bottle Opener Clip"	1
"Panama"	"Google Toddler Raglan Shirt Blue Heather/Navy"	1
"Panama"	"Google Men's Convertible Vest-Jacket Pewter"	1
"Panama"	"YouTube Luggage Tag"	1
"Panama"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	1
"Panama"	"Google Men's Short Sleeve Hero Tee Light Blue"	1
"Panama"	"Android Sticker Sheet Ultra Removable"	1
"Papua New Guinea"	"YouTube Custom Decals"	1
"Papua New Guinea"	"YouTube Hard Cover Journal"	1
"Paraguay"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"Peru"	"22 oz YouTube Bottle Infuser"	2
"Peru"	"Compact Selfie Stick"	2
"Philippines"	"YouTube RFID Journal"	3
"Philippines"	"YouTube Twill Cap"	3
"Philippines"	"Google Men's Convertible Vest-Jacket Pewter"	3
"Philippines"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	3
"Philippines"	"YouTube Men's Short Sleeve Hero Tee White"	3
"Philippines"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	3
"Philippines"	"YouTube Notebook and Pen Set"	3
"Poland"	"YouTube Men's Vintage Tank"	3
"Portugal"	"YouTube RFID Journal"	2
"Portugal"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	2
"Puerto Rico"	"Four Color Retractable Pen"	1
"Puerto Rico"	"Waterproof Backpack"	1
"Puerto Rico"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Puerto Rico"	"YouTube Custom Decals"	1
"Puerto Rico"	"Retractable Ballpoint Pen Red"	1
"Puerto Rico"	"Android Men's Short Sleeve Tri-blend Hero Tee Grey"	1
"Puerto Rico"	"Google Toddler Short Sleeve T-shirt Grey"	1
"Puerto Rico"	"Google Laptop and Cell Phone Stickers"	1
"Puerto Rico"	"Google 3/4 Sleeve Raglan Badge Henley Black"	1
"Qatar"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Qatar"	"Compact Journal with Recycled Pages"	1
"Qatar"	"Google Men's Short Sleeve Performance Badge Tee Navy"	1
"Qatar"	"Google 5-Panel Cap"	1
"Réunion"	"YouTube Custom Decals"	1
"Réunion"	"Google Doodle Decal"	1
"Romania"	"YouTube Custom Decals"	5
"Russia"	"YouTube Twill Cap"	4
"Russia"	"Google Women's Fleece Hoodie"	4
"Rwanda"	"YouTube Wool Heather Cap Heather/Black"	1
"San Marino"	"Google Device Holder Sticky Pad"	1
"San Marino"	"22 oz YouTube Bottle Infuser"	1
"Saudi Arabia"	"24 oz USA Made Aluminum Bottle"	1
"Saudi Arabia"	"Google Women's Vintage Hero Tee Platinum"	1
"Saudi Arabia"	"Leatherette Journal"	1
"Saudi Arabia"	"Google 3/4 Sleeve Raglan Badge Henley Black"	1
"Saudi Arabia"	"YouTube Men's Vintage Tank"	1
"Saudi Arabia"	"24 oz YouTube Sergeant Stripe Bottle"	1
"Saudi Arabia"	"Red Spiral Google Notebook"	1
"Saudi Arabia"	"YouTube Twill Cap"	1
"Saudi Arabia"	"Women's YouTube Short Sleeve Hero Tee Black"	1
"Saudi Arabia"	"Android Men's Short Sleeve Hero Tee White"	1
"Saudi Arabia"	"Google Youth Short Sleeve T-shirt Yellow"	1
"Saudi Arabia"	"Google 17oz Stainless Steel Sport Bottle"	1
"Saudi Arabia"	"Google Rucksack"	1
"Saudi Arabia"	"Android Glass Water Bottle with Black Sleeve"	1
"Saudi Arabia"	"Google Women's Lightweight Microfleece Jacket"	1
"Serbia"	"YouTube Twill Cap"	2
"Singapore"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	5
"Sint Maarten"	"Google Stylus Pen w/ LED Light"	1
"Slovakia"	"YouTube RFID Journal"	2
"Slovakia"	"YouTube Twill Cap"	2
"Slovakia"	"YouTube Hard Cover Journal"	2
"Slovakia"	"YouTube Custom Decals"	2
"Slovakia"	"YouTube Trucker Hat"	2
"Slovakia"	"YouTube Luggage Tag"	2
"Slovenia"	"You Tube Toddler Short Sleeve Tee Red"	1
"Slovenia"	"Electronics Accessory Pouch"	1
"Slovenia"	"YouTube Leatherette Notebook Combo"	1
"Slovenia"	"YouTube Women's Short Sleeve Crew Tee"	1
"Slovenia"	"22 oz YouTube Bottle Infuser"	1
"Slovenia"	"Google G Noise-reducing Bluetooth Headphones"	1
"Slovenia"	"Google Wool Heather Cap Heather/Navy"	1
"Slovenia"	"YouTube Trucker Hat"	1
"Slovenia"	"YouTube Hard Cover Journal"	1
"Somalia"	"YouTube Men's Short Sleeve Hero Tee White"	1
"South Africa"	"YouTube Men's Vintage Henley"	2
"South Africa"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	2
"South Korea"	"Ballpoint Stylus Pen"	2
"South Korea"	"22 oz YouTube Bottle Infuser"	2
"South Korea"	"YouTube Custom Decals"	2
"Spain"	"YouTube RFID Journal"	4
"Spain"	"YouTube Men's Short Sleeve Hero Tee White"	4
"Spain"	"22 oz YouTube Bottle Infuser"	4
"Sri Lanka"	"Android Men's Vintage Henley"	2
"Sri Lanka"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	2
"Sudan"	"YouTube RFID Journal"	1
"Sudan"	"Android Men's Vintage Tee"	1
"Sweden"	"YouTube Leatherette Notebook Combo"	3
"Sweden"	"YouTube Men's Vintage Henley"	3
"Sweden"	"YouTube Men's Short Sleeve Hero Tee White"	3
"Switzerland"	"Sport Bag"	3
"Switzerland"	"Google G Noise-reducing Bluetooth Headphones"	3
"Switzerland"	"YouTube Twill Cap"	3
"Taiwan"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	6
"Tanzania"	"YouTube Twill Cap"	1
"Thailand"	"YouTube Men's Vintage Henley"	4
"Trinidad & Tobago"	"YouTube Hard Cover Journal"	1
"Trinidad & Tobago"	"YouTube Twill Cap"	1
"Tunisia"	"Google Device Holder Sticky Pad"	2
"Turkey"	"YouTube Hard Cover Journal"	4
"Uganda"	"Google Laptop and Cell Phone Stickers"	1
"Uganda"	"Google Women's Insulated Thermal Vest Navy"	1
"Ukraine"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	3
"Ukraine"	"YouTube Hard Cover Journal"	3
"United Arab Emirates"	"Pen Pencil & Highlighter Set"	1
"United Arab Emirates"	"Google Men's Short Sleeve Performance Badge Tee Navy"	1
"United Arab Emirates"	"Google Water Resistant Bluetooth Speaker"	1
"United Arab Emirates"	"Recycled Mouse Pad"	1
"United Arab Emirates"	"Google Toddler Tee Fruit Games Cherries"	1
"United Arab Emirates"	"22 oz YouTube Bottle Infuser"	1
"United Arab Emirates"	"Google Women's Convertible Vest-Jacket Sea Foam Green"	1
"United Arab Emirates"	"YouTube Wool Heather Cap Heather/Black"	1
"United Arab Emirates"	"Android Men's  Zip Hoodie"	1
"United Arab Emirates"	"Google Women's Long Sleeve Tee Lavender"	1
"United Arab Emirates"	"Google Men's Short Sleeve Performance Badge Tee Pewter"	1
"United Arab Emirates"	"Android Rise 14 oz Mug"	1
"United Arab Emirates"	"YouTube Men's Long & Lean Tee Charcoal"	1
"United Arab Emirates"	"Leatherette Journal"	1
"United Arab Emirates"	"Google Women's Fleece Hoodie"	1
"United Arab Emirates"	"Android Youth Short Sleeve T-shirt Aqua"	1
"United Arab Emirates"	"Google Men's Vintage Tank"	1
"United Arab Emirates"	"YouTube Hard Cover Journal"	1
"United Arab Emirates"	"Android Glass Water Bottle with Black Sleeve"	1
"United Kingdom"	"22 oz YouTube Bottle Infuser"	22
"United States"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	169
"Uruguay"	"YouTube Men's Vintage Tank"	2
"Venezuela"	"YouTube Hard Cover Journal"	2
"Venezuela"	"YouTube Twill Cap"	2
"Vietnam"	"YouTube Spiral Journal with Pen"	3
"Zimbabwe"	"Galaxy Screen Cleaning Cloth"	1


Most popular items by City

"(not set)"	"Sport Bag"	9
"(not set)"	"YouTube Custom Decals"	9
"(not set)"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	9
"Adelaide"	"Google Men's Watershed Full Zip Hoodie Grey"	1
"Ahmedabad"	"Google Trucker Hat"	1
"Ahmedabad"	"Google Snapback Hat Black"	1
"Ahmedabad"	"YouTube Leatherette Notebook Combo"	1
"Ahmedabad"	"YouTube Wool Heather Cap Heather/Black"	1
"Ahmedabad"	"Android Men's Vintage Tank"	1
"Ahmedabad"	"Google Tri-blend Hoodie Grey"	1
"Ahmedabad"	"YouTube RFID Journal"	1
"Ahmedabad"	"Women's YouTube Short Sleeve Hero Tee Black"	1
"Ahmedabad"	"YouTube Trucker Hat"	1
"Ahmedabad"	"Google Canvas Tote Natural/Navy"	1
"Ahmedabad"	"Google Laptop and Cell Phone Stickers"	1
"Ahmedabad"	"Google Youth Short Sleeve T-shirt Royal Blue"	1
"Akron"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"Alexandria"	"Google Men's Long Sleeve Pullover Badge Tee Heather"	1
"Amã"	"Softsided Travel Pouch Set"	1
"Amsterdam"	"Google Tube Power Bank"	1
"Amsterdam"	"Android Rise 14 oz Mug"	1
"Amsterdam"	"YouTube Luggage Tag"	1
"Amsterdam"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"Amsterdam"	"Google Canvas Tote Natural/Navy"	1
"Amsterdam"	"YouTube Hard Cover Journal"	1
"Amsterdam"	"Retractable Ballpoint Pen Red"	1
"Amsterdam"	"Google Doodle Decal"	1
"Amsterdam"	"YouTube Men's Vintage Henley"	1
"Amsterdam"	"Aluminum Handy Emergency Flashlight"	1
"Amsterdam"	"Google Device Holder Sticky Pad"	1
"Amsterdam"	"Google Men's Long Sleeve Raglan Ocean Blue"	1
"Ann Arbor"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	2
"Ann Arbor"	"Google Men's Vintage Badge Tee Black"	2
"Ann Arbor"	"Google Men's Convertible Vest-Jacket Pewter"	2
"Antalya"	"Google Tube Power Bank"	1
"Antwerp"	"Colored Pencil Set"	1
"Antwerp"	"Google Insulated Stainless Steel Bottle"	1
"Appleton"	"Aluminum Handy Emergency Flashlight"	1
"Ashburn"	"Compact Selfie Stick"	1
"Ashburn"	"Android Lunch Kit"	1
"Asuncion"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"Athens"	"Electronics Accessory Pouch"	1
"Athens"	"Sport Bag"	1
"Athens"	"22 oz YouTube Bottle Infuser"	1
"Athens"	"Android Men's Short Sleeve Hero Tee Heather"	1
"Athens"	"YouTube Hard Cover Journal"	1
"Atlanta"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	4
"Auckland"	"Google G Noise-reducing Bluetooth Headphones"	1
"Auckland"	"Google Women's Lightweight Microfleece Jacket"	1
"Austin"	"Google Men's Vintage Badge Tee Black"	3
"Austin"	"Google Women's Short Sleeve Shirt Blue"	3
"Austin"	"22 oz Android Bottle"	3
"Avon"	"Bottle Opener Clip"	1
"Avon"	"Google Blackout Cap"	1
"Bandung"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	1
"Bangkok"	"Keyboard DOT Sticker"	2
"Bangkok"	"YouTube Men's Vintage Henley"	2
"Barcelona"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Barcelona"	"Google Tri-blend Hoodie Grey"	1
"Barcelona"	"YouTube Men's Vintage Henley"	1
"Barcelona"	"24 oz YouTube Sergeant Stripe Bottle"	1
"Barcelona"	"Android Hard Cover Journal"	1
"Barcelona"	"YouTube Men's Long & Lean Tee Charcoal"	1
"Barcelona"	"Google Laptop and Cell Phone Stickers"	1
"Barcelona"	"Google Men's Pullover Hoodie Grey"	1
"Barcelona"	"Google Snapback Hat Black"	1
"Barcelona"	"Android Onesie Gold"	1
"Barcelona"	"26 oz Double Wall Insulated Bottle"	1
"Barcelona"	"Waze Mobile Phone Vent Mount"	1
"Barcelona"	"YouTube Men's Short Sleeve Hero Tee White"	1
"Barcelona"	"Android Rise 14 oz Mug"	1
"Barcelona"	"Badge Holder"	1
"Barcelona"	"YouTube Luggage Tag"	1
"Barcelona"	"Google Rucksack"	1
"Barcelona"	"Digital Lightshow Smart Speaker and Notification Center"	1
"Beijing"	"Google Men's Airflow 1/4 Zip Pullover Lapis"	1
"Belgrade"	"Insulated Bottle"	1
"Bellflower"	"YouTube Custom Decals"	2
"Bellingham"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	1
"Bellingham"	"YouTube Custom Decals"	1
"Belo Horizonte"	"Collapsible Shopping Bag"	1
"Bengaluru"	"Women's YouTube Short Sleeve Hero Tee Black"	2
"Bengaluru"	"Google Laptop and Cell Phone Stickers"	2
"Bengaluru"	"YouTube Spiral Journal with Pen"	2
"Bengaluru"	"YouTube Men's Vintage Henley"	2
"Bengaluru"	"Google Men's 100% Cotton Short Sleeve Hero Tee Red"	2
"Bengaluru"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	2
"Bengaluru"	"Google Men's Vintage Badge Tee Black"	2
"Bengaluru"	"Android Men's Short Sleeve Hero Tee White"	2
"Bengaluru"	"YouTube Men's Short Sleeve Hero Tee Black"	2
"Bengaluru"	"YouTube Leatherette Notebook Combo"	2
"Bengaluru"	"YouTube Onesie Heather"	2
"Berkeley"	"YouTube Onesie Heather"	1
"Berlin"	"YouTube Wool Heather Cap Heather/Black"	3
"Bhubaneswar"	"20 oz Stainless Steel Insulated Tumbler"	1
"Bilbao"	"Google Women's Short Sleeve Hero Tee Sky Blue"	1
"Bilbao"	"Google Trucker Hat"	1
"Bogota"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Bogota"	"Google Women's Fleece Hoodie"	1
"Bogota"	"Google Laptop Backpack"	1
"Bogota"	"Metal Earbuds with Small Zipper Case"	1
"Bogota"	"Google Flashlight"	1
"Bogota"	"Micro Wireless Earbud"	1
"Bogota"	"Google Doodle Decal"	1
"Bogota"	"SPF-15 Slim & Slender Lip Balm"	1
"Bogota"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	1
"Bogota"	"YouTube Men's Short Sleeve Hero Tee White"	1
"Bogota"	"Google Snapback Hat Black"	1
"Bogota"	"YouTube Men's 3/4 Sleeve Henley"	1
"Bogota"	"You Tube Toddler Short Sleeve Tee Red"	1
"Bogota"	"Android Men's Vintage Henley"	1
"Bogota"	"Google Men's Microfiber 1/4 Zip Pullover Blue/Indigo"	1
"Bogota"	"YouTube Hard Cover Journal"	1
"Bogota"	"Google Men's Pullover Hoodie"	1
"Bogota"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"Bogota"	"Google Men's Airflow 1/4 Zip Pullover Black"	1
"Bogota"	"Google Lunch Bag"	1
"Bordeaux"	"YouTube Luggage Tag"	1
"Boston"	"Google Men's Performance Polo Grey/Black"	2
"Boston"	"Collapsible Shopping Bag"	2
"Boston"	"Google Laptop Backpack"	2
"Boulder"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Boulder"	"Google Men's Performance Polo Grey/Black"	1
"Boulder"	"Compact Selfie Stick"	1
"Boulder"	"Google Men's Performance Full Zip Jacket Black"	1
"Boulder"	"YouTube Twill Cap"	1
"Boulder"	"4-in-1 Carabiner Charger"	1
"Boulder"	"Bottle Opener Clip"	1
"Boulder"	"You Tube Toddler Short Sleeve Tee Red"	1
"Bratislava"	"Nest® Learning Thermostat 3rd Gen-USA - Copper"	1
"Brisbane"	"YouTube Twill Cap"	1
"Brisbane"	"YouTube Notebook and Pen Set"	1
"Brisbane"	"YouTube Leatherette Notebook Combo"	1
"Brisbane"	"Google Women's Short Sleeve Hero Tee Red Heather"	1
"Brisbane"	"Google Men's Microfiber 1/4 Zip Pullover Blue/Indigo"	1
"Brisbane"	"24 oz YouTube Sergeant Stripe Bottle"	1
"Brisbane"	"Google Youth Girl Hoodie"	1
"Brisbane"	"Google Men's Softshell Jacket Black/Grey"	1
"Brisbane"	"Android 24 oz Contigo Bottle"	1
"Brisbane"	"Google High Capacity 10,400mAh Charger"	1
"Brisbane"	"YouTube Wool Heather Cap Heather/Black"	1
"Brisbane"	"YouTube Men's Vintage Henley"	1
"Brno"	"Google Men's Pullover Hoodie Grey"	1
"Brno"	"Leatherette Journal"	1
"Brussels"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Bucharest"	"16 oz. Hot/Cold Tumbler"	1
"Bucharest"	"Google Accent Insulated Stainless Steel Bottle"	1
"Bucharest"	"YouTube Custom Decals"	1
"Bucharest"	"Google Men's 100% Cotton Short Sleeve Hero Tee Red"	1
"Bucharest"	"Google Women's V-Neck Tee Grey"	1
"Bucharest"	"Google Wool Heather Cap Heather/Navy"	1
"Bucharest"	"Android Rise 14 oz Mug"	1
"Bucharest"	"YouTube Twill Cap"	1
"Bucharest"	"Google Men's Heavyweight Long Sleeve Hero Tee Navy"	1
"Bucharest"	"YouTube Men's 3/4 Sleeve Henley"	1
"Bucharest"	"Oasis Backpack"	1
"Bucharest"	"Google Canvas Tote Natural/Navy"	1
"Bucharest"	"Google Men's Convertible Vest-Jacket Pewter"	1
"Budapest"	"Google Men's Short Sleeve Performance Badge Tee Pewter"	1
"Budapest"	"Google Men's Long Sleeve Raglan Ocean Blue"	1
"Budapest"	"Recycled Mouse Pad"	1
"Budapest"	"Android Men's  Zip Hoodie"	1
"Budapest"	"Google Men's Pullover Hoodie Grey"	1
"Budapest"	"YouTube Wool Heather Cap Heather/Black"	1
"Buenos Aires"	"Google Alpine Style Backpack"	1
"Buenos Aires"	"Google Women's V-Neck Tee Grey"	1
"Buenos Aires"	"Google Men's Skater Tee Charcoal"	1
"Buenos Aires"	"Android Men's  Zip Hoodie"	1
"Buenos Aires"	"Sport Bag"	1
"Buenos Aires"	"Rocket Flashlight"	1
"Buenos Aires"	"24 oz YouTube Sergeant Stripe Bottle"	1
"Buenos Aires"	"Google Ballpoint Pen Black"	1
"Buenos Aires"	"Google Laptop Tech Backpack"	1
"Burnaby"	"Android Men's  Zip Hoodie"	1
"Burnaby"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Burnaby"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Calgary"	"Android Toddler Short Sleeve T-shirt Pink"	1
"Calgary"	"YouTube Leatherette Notebook Combo"	1
"Calgary"	"YouTube Luggage Tag"	1
"Calgary"	"Android Wool Heather Cap Heather/Black"	1
"Calgary"	"Recycled Mouse Pad"	1
"Cambridge"	"Galaxy Screen Cleaning Cloth"	3
"Cape Town"	"Google Men's Pullover Hoodie Grey"	1
"Chandigarh"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"Charlotte"	"Android Men's  Zip Hoodie"	2
"Charlotte"	"7&quot; Dog Frisbee"	2
"Charlottetown"	"Google Ballpoint Pen Black"	1
"Chennai"	"22 oz YouTube Bottle Infuser"	5
"Chennai"	"YouTube RFID Journal"	5
"Chicago"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	7
"Chico"	"YouTube Men's 3/4 Sleeve Henley"	1
"Chico"	"Android Women's Racer Back Tank Black"	1
"Chico"	"Android Men's Long Sleeve Badge Crew Tee Heather"	1
"Chiyoda"	"24 oz YouTube Sergeant Stripe Bottle"	1
"Chuo"	"Google Women's Scoop Neck Tee Black"	1
"Cluj-Napoca"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Colombo"	"Android Men's Vintage Henley"	2
"Columbia"	"Google Device Stand"	1
"Columbus"	"Android Men's  Zip Hoodie"	1
"Columbus"	"Google Men's Short Sleeve Badge Tee Charcoal"	1
"Copenhagen"	"Rocket Flashlight"	1
"Copenhagen"	"Google Trucker Hat"	1
"Cork"	"YouTube Custom Decals"	1
"Council Bluffs"	"Google Men's Long Sleeve Raglan Ocean Blue"	1
"Council Bluffs"	"Google Kick Ball"	1
"Courbevoie"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Courbevoie"	"Android Wool Heather Cap Heather/Black"	1
"Coventry"	"YouTube Youth Short Sleeve Tee Red"	1
"Culiacan"	"Google Women's 1/4 Zip Performance Pullover Two-Tone Blue"	1
"Culiacan"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Culiacan"	"20 oz Stainless Steel Insulated Tumbler"	1
"Cupertino"	"YouTube Men's Vintage Tank"	2
"Dallas"	"Google Women's Short Sleeve Hero Tee Black"	2
"Dallas"	"Micro Wireless Earbud"	2
"Dallas"	"YouTube Leatherette Notebook Combo"	2
"Denver"	"Google Twill Cap"	1
"Denver"	"Crunch Noise Dog Toy"	1
"Denver"	"Google 4400mAh Power Bank"	1
"Denver"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Denver"	"Google Luggage Tag"	1
"Denver"	"Waterproof Backpack"	1
"Detroit"	"Google High Capacity 10,400mAh Charger"	1
"Detroit"	"Google Wool Heather Cap Heather/Navy"	1
"Detroit"	"Google 22 oz Water Bottle"	1
"Detroit"	"Galaxy Screen Cleaning Cloth"	1
"Doha"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Druid Hills"	"Colored Pencil Set"	1
"Dubai"	"Google Toddler Tee Fruit Games Cherries"	1
"Dubai"	"Google Men's Short Sleeve Performance Badge Tee Pewter"	1
"Dubai"	"Google Women's Convertible Vest-Jacket Sea Foam Green"	1
"Dubai"	"YouTube Hard Cover Journal"	1
"Dubai"	"Leatherette Journal"	1
"Dubai"	"Google Women's Long Sleeve Tee Lavender"	1
"Dubai"	"Recycled Mouse Pad"	1
"Dubai"	"Android Youth Short Sleeve T-shirt Aqua"	1
"Dubai"	"Android Glass Water Bottle with Black Sleeve"	1
"Dublin"	"You Tube Toddler Short Sleeve Tee Red"	3
"East Lansing"	"25L Classic Rucksack"	1
"Eau Claire"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	2
"Eau Claire"	"Google Women's Short Sleeve Badge Tee Red Heather"	2
"Eau Claire"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	2
"Edmonton"	"YouTube Notebook and Pen Set"	1
"Edmonton"	"Google Collapsible Pet Bowl"	1
"Edmonton"	"Google Stylus Pen w/ LED Light"	1
"Egham"	"YouTube Men's Short Sleeve Hero Tee White"	1
"El Paso"	"Google Men's Short Sleeve Hero Tee Light Blue"	1
"Esbjerg"	"Google Women's Short Sleeve Badge Tee Red Heather"	1
"Forest Park"	"Google Men's Microfiber 1/4 Zip Pullover Blue/Indigo"	1
"Fort Worth"	"YouTube Trucker Hat"	1
"Fortaleza"	"Android Luggage Tag"	1
"Frankfurt"	"Google Bib White"	1
"Fremont"	"Android Rise 14 oz Mug"	1
"Fremont"	"Google Laptop Backpack"	1
"Fremont"	"Google Infuser-Top Water Bottle"	1
"Fremont"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Fremont"	"Android Men's  Zip Hoodie"	1
"Fremont"	"Google Women's Vintage Hero Tee Black"	1
"Fremont"	"Colored Pencil Set"	1
"Fremont"	"Google Tri-blend Hoodie Grey"	1
"Fremont"	"Reusable Shopping Bag"	1
"Fremont"	"Google Stylus Pen w/ LED Light"	1
"Fremont"	"Nest® Protect Smoke + CO White Battery Alarm-USA"	1
"Fremont"	"24 oz USA Made Aluminum Bottle"	1
"Fremont"	"Nest® Protect Smoke + CO Black Wired Alarm-USA"	1
"Fremont"	"Google Men's Convertible Vest-Jacket Pewter"	1
"Fremont"	"Google 2200mAh Micro Charger"	1
"Fremont"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Fremont"	"Windup Android"	1
"Fremont"	"YouTube RFID Journal"	1
"Ghent"	"Windup Android"	1
"Glasgow"	"Digital Lightshow Smart Speaker and Notification Center"	1
"Glasgow"	"Google Water Resistant Bluetooth Speaker"	1
"Goose Creek"	"Android Wool Heather Cap Heather/Black"	1
"Gothenburg"	"Google Women's Short Sleeve Performance Tee Navy"	1
"Greer"	"Google Men's Quilted Insulated Vest Black"	1
"Gurgaon"	"Android Men's Vintage Tank"	2
"Hamburg"	"Suitcase Organizer Cubes"	1
"Hamburg"	"Basecamp Explorer Powerbank Flashlight"	1
"Hamburg"	"Google Blackout Cap"	1
"Hamburg"	"22 oz Android Bottle"	1
"Hamburg"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Hamburg"	"22 oz YouTube Bottle Infuser"	1
"Hamburg"	"Android Rise 14 oz Mug"	1
"Hamburg"	"Google Women's Performance Hero Tee Gunmetal"	1
"Hamburg"	"Android BTTF Moonshot Graphic Tee"	1
"Hamburg"	"YouTube Notebook and Pen Set"	1
"Hamburg"	"YouTube RFID Journal"	1
"Hamburg"	"Google Water Resistant Bluetooth Speaker"	1
"Hanoi"	"YouTube Luggage Tag"	1
"Hanoi"	"Waze Dress Socks"	1
"Hanoi"	"26 oz Double Wall Insulated Bottle"	1
"Hanoi"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Hayward"	"Google Men's Vintage Badge Tee White"	1
"Helsinki"	"Android Onesie Gold"	1
"Ho Chi Minh City"	"Basecamp Explorer Powerbank Flashlight"	1
"Ho Chi Minh City"	"Google Men's Short Sleeve Badge Tee Charcoal"	1
"Ho Chi Minh City"	"16 oz. Hot and Cold Tumbler"	1
"Ho Chi Minh City"	"Google Men's Performance Polo Grey/Black"	1
"Ho Chi Minh City"	"You Tube Toddler Short Sleeve Tee Red"	1
"Ho Chi Minh City"	"Google Women's Zip Hoodie Grey"	1
"Ho Chi Minh City"	"Keyboard DOT Sticker"	1
"Ho Chi Minh City"	"22 oz YouTube Bottle Infuser"	1
"Ho Chi Minh City"	"YouTube Spiral Journal with Pen"	1
"Ho Chi Minh City"	"Google Accent Insulated Stainless Steel Bottle"	1
"Ho Chi Minh City"	"Google Laptop Backpack"	1
"Ho Chi Minh City"	"Google Men's Airflow 1/4 Zip Pullover Black"	1
"Ho Chi Minh City"	"Recycled Mouse Pad"	1
"Ho Chi Minh City"	"Android Men's Long Sleeve Badge Crew Tee Heather"	1
"Hong Kong"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	2
"Hong Kong"	"Galaxy Screen Cleaning Cloth"	2
"Hong Kong"	"Recycled Mouse Pad"	2
"Houston"	"Google Men's Long Sleeve Raglan Ocean Blue"	3
"Houston"	"YouTube Men's Short Sleeve Hero Tee Black"	3
"Hyderabad"	"Google Men's Vintage Badge Tee Black"	5
"Hyderabad"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	5
"Iasi"	"YouTube Men's Vintage Henley"	1
"Indianapolis"	"Ballpoint Stylus Pen"	1
"Indore"	"YouTube Leatherette Notebook Combo"	1
"Indore"	"YouTube Men's Short Sleeve Hero Tee White"	1
"Indore"	"Google Twill Cap"	1
"Indore"	"YouTube Hard Cover Journal"	1
"Indore"	"YouTube Trucker Hat"	1
"Ipoh"	"Bottle Opener Clip"	1
"Irvine"	"Google Women's Scoop Neck Tee Black"	1
"Irvine"	"Google Tote Bag"	1
"Irvine"	"YouTube Trucker Hat"	1
"Irvine"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	1
"Irvine"	"Google High Capacity 10,400mAh Charger"	1
"Irvine"	"Google Laptop and Cell Phone Stickers"	1
"Irvine"	"Google Women's Short Sleeve Hero Tee Black"	1
"Irvine"	"Stadium Cups-Sets of 4"	1
"Irvine"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Irvine"	"Google Women's Convertible Vest-Jacket Sea Foam Green"	1
"Irvine"	"Sport Bag"	1
"Irvine"	"Google Wool Heather Cap Heather/Navy"	1
"Irvine"	"Google 5-Panel Cap"	1
"Irvine"	"16 oz. Hot and Cold Tumbler"	1
"Irvine"	"Android Rise 14 oz Mug"	1
"Irvine"	"Android 17oz Stainless Steel Sport Bottle"	1
"Irvine"	"Recycled Mouse Pad"	1
"Irvine"	"Pop-a-Point Crayon"	1
"Irvine"	"Google Men's Vintage Badge Tee Sage"	1
"Irvine"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	1
"Istanbul"	"Android Sticker Sheet Ultra Removable"	2
"Istanbul"	"Google G Noise-reducing Bluetooth Headphones"	2
"Izmir"	"Google Women's Short Sleeve Hero Tee White"	1
"Izmir"	"Google Women's Short Sleeve Hero Tee Black"	1
"Jacksonville"	"20 oz Stainless Steel Insulated Tumbler"	1
"Jacksonville"	"Google Device Stand"	1
"Jaipur"	"YouTube Spiral Journal with Pen"	1
"Jaipur"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Jaipur"	"Google 5-Panel Snapback Cap"	1
"Jakarta"	"22 oz YouTube Bottle Infuser"	2
"Jakarta"	"Android Journal Book Set"	2
"Jakarta"	"YouTube Men's Short Sleeve Hero Tee White"	2
"Jakarta"	"Google Women's 1/4 Zip Jacket Charcoal"	2
"Jersey City"	"Google 25 oz Red Stainless Steel Bottle"	1
"Jersey City"	"Google Women's Short Sleeve Hero Dark Grey"	1
"Jersey City"	"Google Youth Short Sleeve Tee White"	1
"Jersey City"	"Google Women's 3/4 Sleeve Baseball Raglan Heather/Black"	1
"Jersey City"	"Google Laptop Tech Backpack"	1
"Johnson City"	"Google Infuser-Top Water Bottle"	1
"Kalamazoo"	"Leather and Metal Ballpoint Pen"	1
"Kalamazoo"	"Satin Black Ballpoint Pen"	1
"Kansas City"	"Google Women's Short Sleeve Hero Tee Heather"	1
"Kansas City"	"Google Insulated Stainless Steel Bottle"	1
"Kansas City"	"Google Women's Short Sleeve Hero Tee Red Heather"	1
"Karachi"	"Ballpoint Stylus Pen"	1
"Karachi"	"Google Men's Long Sleeve Raglan Ocean Blue"	1
"Karachi"	"YouTube RFID Journal"	1
"Karachi"	"YouTube Men's Vintage Tee"	1
"Kharagpur"	"Google Tri-blend Hoodie Grey"	1
"Kharagpur"	"Google Men's Performance Full Zip Jacket Black"	1
"Kharkiv"	"Google Women's V-Neck Tee Charcoal"	1
"Kharkiv"	"Bic Digital Clic Stic Stylus Pen"	1
"Kiev"	"Android Rise 14 oz Mug"	1
"Kiev"	"22 oz Android Bottle"	1
"Kiev"	"Google Women's Fleece Hoodie"	1
"Kiev"	"Keyboard DOT Sticker"	1
"Kiev"	"Google Tote Bag"	1
"Kiev"	"YouTube Leatherette Notebook Combo"	1
"Kiev"	"Google Women's Short Sleeve V-Neck Tee Dark Grey"	1
"Kiev"	"Oasis Backpack"	1
"Kiev"	"Metal Texture Roller Pen"	1
"Kiev"	"Google Women's Vintage Hero Tee White"	1
"Kiev"	"Micro Wireless Earbud"	1
"Kiev"	"Waterpoof Gear Bag"	1
"Kiev"	"Google Toddler Short Sleeve Tee White"	1
"Kiev"	"Grip Kit Cable Organizer"	1
"Kiev"	"Google Laptop Tech Backpack"	1
"Kiev"	"Google Bluetooth Speaker-Power Bank"	1
"Kiev"	"Maze Pen"	1
"Kiev"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Kiev"	"Bottle Opener Clip"	1
"Kiev"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Kirkland"	"Google Women's Lightweight Microfleece Jacket"	2
"Kirkland"	"Waterproof Backpack"	2
"Kitchener"	"Recycled Mouse Pad"	1
"Kitchener"	"Digital Lightshow Smart Speaker and Notification Center"	1
"Kitchener"	"Keyboard DOT Sticker"	1
"Kitchener"	"Google 5-Panel Cap"	1
"Kitchener"	"Google Women's Lightweight Microfleece Jacket"	1
"Kitchener"	"Android Men's Long Sleeve Badge Crew Tee Heather"	1
"Kolkata"	"YouTube Custom Decals"	2
"Kolkata"	"Google Phone Sanitizer"	2
"Kolkata"	"YouTube Luggage Tag"	2
"Kolkata"	"YouTube Spiral Journal with Pen"	2
"Kolkata"	"24 oz YouTube Sergeant Stripe Bottle"	2
"Kovrov"	"Google Women's Fleece Hoodie"	1
"Krakow"	"Google Accent Insulated Stainless Steel Bottle"	1
"Krakow"	"Android Glass Water Bottle with Black Sleeve"	1
"Kuala Lumpur"	"Google Men's Performance Polo Grey/Black"	1
"Kuala Lumpur"	"Google Men's Short Sleeve Hero Tee Light Blue"	1
"Kuala Lumpur"	"Android Spiral Journal with Pen"	1
"Kuala Lumpur"	"Android Men's  Zip Hoodie"	1
"Kuala Lumpur"	"Google Women's Convertible Vest-Jacket Sea Foam Green"	1
"Kuala Lumpur"	"Google Slim Utility Travel Bag"	1
"Kuala Lumpur"	"Electronics Accessory Pouch"	1
"Kuala Lumpur"	"Google Alpine Style Backpack"	1
"Kuala Lumpur"	"YouTube Wool Heather Cap Heather/Black"	1
"Kuala Lumpur"	"26 oz Double Wall Insulated Bottle"	1
"Kuala Lumpur"	"Google Men's Short Sleeve Hero Tee Heather"	1
"Kuala Lumpur"	"YouTube Leatherette Notebook Combo"	1
"Kuala Lumpur"	"Four Color Retractable Pen"	1
"Kuala Lumpur"	"Google Men's Skater Tee Charcoal"	1
"Kuala Lumpur"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Kuala Lumpur"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	1
"Kuala Lumpur"	"Google Women's Short Sleeve Hero Tee Black"	1
"La Victoria"	"Google Women's Vintage Hero Tee Black"	1
"La Victoria"	"Google Men's Weatherblock Shell Jacket Black"	1
"La Victoria"	"YouTube Twill Cap"	1
"La Victoria"	"Waze Mobile Phone Vent Mount"	1
"La Victoria"	"22 oz YouTube Bottle Infuser"	1
"La Victoria"	"Android Twill Cap"	1
"La Victoria"	"Android Women's Short Sleeve Badge Tee Dark Heather"	1
"La Victoria"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"LaFayette"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"Lahore"	"Google Power Bank"	1
"Lake Oswego"	"Google Women's Short Sleeve Performance Tee Pewter"	1
"Lake Oswego"	"Google Men's 100% Cotton Short Sleeve Hero Tee Red"	1
"Lake Oswego"	"Google Men's Short Sleeve Hero Tee Heather"	1
"Lake Oswego"	"Recycled Mouse Pad"	1
"Lake Oswego"	"Google Men's Performance Full Zip Jacket Black"	1
"Las Vegas"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Las Vegas"	"Google Men's Airflow 1/4 Zip Pullover Lapis"	1
"Las Vegas"	"Android Youth Short Sleeve T-shirt Pewter"	1
"Lisbon"	"Google Blackout Cap"	1
"London"	"YouTube Twill Cap"	9
"Longtan District"	"26 oz Double Wall Insulated Bottle"	1
"Los Angeles"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	7
"Lucknow"	"YouTube Men's Vintage Tank"	1
"Lucknow"	"Android Men's Short Sleeve Tri-blend Hero Tee Grey"	1
"Madison"	"Google Tote Bag"	1
"Madrid"	"SPF-15 Slim & Slender Lip Balm"	2
"Madrid"	"Waze Dress Socks"	2
"Makati"	"Google Men's Short Sleeve Hero Tee Light Blue"	1
"Makati"	"Compact Bluetooth Speaker"	1
"Makati"	"Google G Noise-reducing Bluetooth Headphones"	1
"Manchester"	"YouTube Luggage Tag"	1
"Manchester"	"Google G Noise-reducing Bluetooth Headphones"	1
"Manila"	"YouTube Men's Short Sleeve Hero Tee White"	2
"Maracaibo"	"Google Power Bank"	1
"Maracaibo"	"Compact Bluetooth Speaker"	1
"Maracaibo"	"Android Men's Vintage Henley"	1
"Maracaibo"	"Google Youth Tee Fruit Games Pineapple"	1
"Marlboro"	"Google Men's Microfiber 1/4 Zip Pullover Blue/Indigo"	1
"Marseille"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Marseille"	"YouTube Men's 3/4 Sleeve Henley"	1
"Medellin"	"Google Men's Watershed Full Zip Hoodie Grey"	1
"Medellin"	"Bottle Opener Clip"	1
"Melbourne"	"YouTube Notebook and Pen Set"	3
"Melbourne"	"Google Laptop and Cell Phone Stickers"	3
"Menlo Park"	"Google Snapback Hat Black"	1
"Menlo Park"	"Google Bluetooth Speaker-Power Bank"	1
"Menlo Park"	"Google Men's Weatherblock Shell Jacket Black"	1
"Menlo Park"	"Nest® Cam Indoor Security Camera - USA"	1
"Mexico City"	"Women's YouTube Short Sleeve Hero Tee Black"	2
"Mexico City"	"YouTube RFID Journal"	2
"Mexico City"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	2
"Milan"	"22 oz YouTube Bottle Infuser"	2
"Milpitas"	"Google Women's Quilted Insulated Vest Black"	1
"Milpitas"	"Google Men's Microfiber 1/4 Zip Pullover Blue/Indigo"	1
"Milpitas"	"Google Women's Short Sleeve Shirt Blue"	1
"Milpitas"	"Crunch Noise Dog Toy"	1
"Milpitas"	"Android Stretch Fit Hat"	1
"Milpitas"	"Nest® Protect Smoke + CO Black Battery Alarm-USA"	1
"Milpitas"	"Google Women's Lightweight Microfleece Jacket"	1
"Milpitas"	"Google Men's  Zip Hoodie"	1
"Milpitas"	"Google Women's Short Sleeve Hero Dark Grey"	1
"Minato"	"YouTube Leatherette Notebook Combo"	2
"Minato"	"Windup Android"	2
"Minato"	"Women's YouTube Short Sleeve Hero Tee Black"	2
"Minato"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	2
"Minato"	"YouTube Men's Vintage Tank"	2
"Minneapolis"	"Android Men's Short Sleeve Tri-blend Hero Tee Grey"	1
"Minneapolis"	"Google Men's Vintage Badge Tee White"	1
"Mississauga"	"Android Onesie Baby Blue"	1
"Mississauga"	"Google Toddler Hoodie Royal Blue"	1
"Mississauga"	"Google Power Bank"	1
"Mississauga"	"YouTube Custom Decals"	1
"Mississauga"	"Collapsible Shopping Bag"	1
"Monterrey"	"YouTube Leatherette Notebook Combo"	1
"Monterrey"	"Google Toddler 1/4 Zip Fleece Pewter"	1
"Monterrey"	"Google Trucker Hat"	1
"Montevideo"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Montevideo"	"Google Vintage Henley Grey/Black"	1
"Montreal"	"YouTube RFID Journal"	2
"Montreal"	"Google Alpine Style Backpack"	2
"Montreal"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	2
"Montreuil"	"Colored Pencil Set"	1
"Montreuil"	"Collapsible Shopping Bag"	1
"Moscow"	"Google Long Sleeve Raglan Badge Henley Ocean Blue"	1
"Moscow"	"Google Women's Short Sleeve Hero Tee Black"	1
"Moscow"	"SPF-15 Slim & Slender Lip Balm"	1
"Moscow"	"Google Men's 100% Cotton Short Sleeve Hero Tee Red"	1
"Moscow"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Moscow"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"Moscow"	"Reusable Shopping Bag"	1
"Moscow"	"Google Heavyweight Long Sleeve Hero Tee Burgundy"	1
"Moscow"	"Google Men's Bayside Graphic Tee"	1
"Moscow"	"Google Youth Short Sleeve T-shirt Green"	1
"Moscow"	"Google Women's 1/4 Zip Jacket Charcoal"	1
"Moscow"	"Google Men's Airflow 1/4 Zip Pullover Lapis"	1
"Moscow"	"YouTube Wool Heather Cap Heather/Black"	1
"Moscow"	"YouTube Notebook and Pen Set"	1
"Moscow"	"Google Women's Vintage T-Shirt Black"	1
"Moscow"	"Android Men's Vintage Tank"	1
"Moscow"	"YouTube Youth Short Sleeve Tee Red"	1
"Moscow"	"Google Kick Ball"	1
"Moscow"	"Google Women's Fleece Hoodie"	1
"Mountain View"	"Nest® Cam Indoor Security Camera - USA"	24
"Mumbai"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	3
"Mumbai"	"YouTube Men's Vintage Tank"	3
"Mumbai"	"YouTube Men's Vintage Tee"	3
"Munich"	"YouTube Twill Cap"	2
"Nagoya"	"Google Accent Insulated Stainless Steel Bottle"	1
"Nagoya"	"Google Women's Convertible Vest-Jacket Sea Foam Green"	1
"Nairobi"	"Google Men's Skater Tee Grey"	1
"Nairobi"	"Google Flashlight"	1
"Nanded"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	1
"Nashville"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	1
"Neipu Township"	"Google Women's Fleece Hoodie"	1
"New Delhi"	"Google Men's Short Sleeve Hero Tee Heather"	2
"New Delhi"	"Google Snapback Hat Black"	2
"New Delhi"	"YouTube Men's Vintage Tee"	2
"New Delhi"	"YouTube Custom Decals"	2
"New York"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	14
"Nice"	"24 oz USA Made Aluminum Bottle"	1
"Norfolk"	"YouTube Trucker Hat"	1
"Norfolk"	"Google Women's Short Sleeve Hero Tee Red Heather"	1
"Norfolk"	"Compact Bluetooth Speaker"	1
"Norfolk"	"Suitcase Organizer Cubes"	1
"not available in demo dataset"	"22 oz YouTube Bottle Infuser"	186
"Oakland"	"Reusable Shopping Bag"	1
"Oakland"	"Google Doodle Decal"	1
"Oakland"	"Google Women's Convertible Vest-Jacket Sea Foam Green"	1
"Oakland"	"Keyboard DOT Sticker"	1
"Oakland"	"Android Men's  Zip Hoodie"	1
"Oakland"	"Yoga Block"	1
"Oakland"	"Google Laptop and Cell Phone Stickers"	1
"Oakland"	"Mistral Rucksack"	1
"Odessa"	"Insulated Bottle"	1
"Orlando"	"Women's YouTube Short Sleeve Hero Tee Black"	1
"Orlando"	"Suitcase Organizer Cubes"	1
"Orlando"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Orlando"	"YouTube Wool Heather Cap Heather/Black"	1
"Orlando"	"Google Men's Watershed Full Zip Hoodie Grey"	1
"Orlando"	"YouTube Men's Vintage Henley"	1
"Osaka"	"Google Infant Short Sleeve Tee Red"	1
"Osaka"	"Waze Dress Socks"	1
"Osaka"	"Nest® Protect Smoke + CO White Wired Alarm-USA"	1
"Osaka"	"Google Stylus Pen w/ LED Light"	1
"Osaka"	"Google Rucksack"	1
"Osaka"	"Google Women's Short Sleeve Hero Tee Sky Blue"	1
"Oslo"	"Google Men's 100% Cotton Short Sleeve Hero Tee Red"	1
"Ottawa"	"YouTube Men's Short Sleeve Hero Tee White"	1
"Ottawa"	"Google Men's Short Sleeve Hero Tee Heather"	1
"Oviedo"	"Google Luggage Tag"	1
"Oviedo"	"YouTube Wool Heather Cap Heather/Black"	1
"Oviedo"	"Google 5-Panel Snapback Cap"	1
"Oxford"	"Google Water Resistant Bluetooth Speaker"	1
"Palo Alto"	"Nest® Cam Outdoor Security Camera - USA"	7
"Panama City"	"Google Men's Convertible Vest-Jacket Pewter"	1
"Paris"	"YouTube Trucker Hat"	3
"Paris"	"Google Men's Performance Polo Grey/Black"	3
"Parsippany-Troy Hills"	"Google Men's Lightweight Microfleece Jacket Black"	1
"Patna"	"22 oz Android Bottle"	1
"Patna"	"Google Vintage Henley Grey/Black"	1
"Perth"	"Google Rucksack"	1
"Perth"	"YouTube Custom Decals"	1
"Perth"	"Google Women's Short Sleeve Hero Tee Red Heather"	1
"Perth"	"Women's YouTube Short Sleeve Hero Tee Black"	1
"Perth"	"YouTube Leatherette Notebook Combo"	1
"Petaling Jaya"	"Google Women's 1/4 Zip Performance Pullover Black"	1
"Petaling Jaya"	"Google Women's Short Sleeve Hero Tee Black"	1
"Philadelphia"	"Android Men's Vintage Tee"	1
"Philadelphia"	"Google Vintage Henley Grey/Black"	1
"Philadelphia"	"Google Women's Vintage Hero Tee White"	1
"Philadelphia"	"Android Women's Fleece Hoodie"	1
"Philadelphia"	"Google Women's Short Sleeve Shirt Blue"	1
"Philadelphia"	"Google Men's Bayside Graphic Tee"	1
"Philadelphia"	"YouTube Men's Short Sleeve Hero Tee White"	1
"Philadelphia"	"Android Wool Heather Cap Heather/Black"	1
"Philadelphia"	"Google Women's Short Sleeve Hero Tee Black"	1
"Philadelphia"	"YouTube Youth Short Sleeve Tee Red"	1
"Philadelphia"	"Micro Wireless Earbud"	1
"Philadelphia"	"Metal Earbuds with Small Zipper Case"	1
"Philadelphia"	"Android Glass Water Bottle with Black Sleeve"	1
"Philadelphia"	"Galaxy Screen Cleaning Cloth"	1
"Philadelphia"	"YouTube Spiral Journal with Pen"	1
"Philadelphia"	"Google Onesie Royal Blue"	1
"Philadelphia"	"Leatherette Journal"	1
"Philadelphia"	"Google High Capacity 10,400mAh Charger"	1
"Philadelphia"	"Google Men's Vintage Tank"	1
"Philadelphia"	"Google 5-Panel Cap"	1
"Philadelphia"	"YouTube Men's Vintage Tank"	1
"Philadelphia"	"Google Men's Watershed Full Zip Hoodie Grey"	1
"Philadelphia"	"Google Wool Heather Cap Heather/Navy"	1
"Philadelphia"	"Google Tote Bag"	1
"Philadelphia"	"YouTube Twill Cap"	1
"Philadelphia"	"Google Lunch Bag"	1
"Phoenix"	"Google Men's Performance Polo Grey/Black"	1
"Phoenix"	"Switch Tone Color Crayon Pen"	1
"Phoenix"	"Google Men's Short Sleeve Hero Tee Heather"	1
"Phoenix"	"Nest® Cam Indoor Security Camera - USA"	1
"Phoenix"	"Android 5-Panel Low Cap"	1
"Phoenix"	"Galaxy Screen Cleaning Cloth"	1
"Phoenix"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"Piscataway Township"	"Google Men's 100% Cotton Short Sleeve Hero Tee Red"	1
"Pittsburgh"	"Collapsible Shopping Bag"	2
"Pleasanton"	"Electronics Accessory Pouch"	1
"Portland"	"YouTube Notebook and Pen Set"	1
"Portland"	"YouTube Trucker Hat"	1
"Portland"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"Portland"	"Google PowerKit"	1
"Portland"	"YouTube Men's Short Sleeve Hero Tee Charcoal"	1
"Portland"	"Google Tri-blend Hoodie Grey"	1
"Portland"	"Google Women's Convertible Vest-Jacket Sea Foam Green"	1
"Portland"	"Google Women's 1/4 Zip Jacket Charcoal"	1
"Poznan"	"Android Journal Book Set"	1
"Poznan"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Poznan"	"Google Bongo Cupholder Bluetooth Speaker"	1
"Pozuelo de Alarcon"	"Engraved Ceramic Google Mug"	1
"Pozuelo de Alarcon"	"Google Car Clip Phone Holder"	1
"Pozuelo de Alarcon"	"YouTube Hard Cover Journal"	1
"Pozuelo de Alarcon"	"Rocket Flashlight"	1
"Prague"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	2
"Pune"	"Google Women's 3/4 Sleeve Baseball Raglan Heather/Black"	2
"Pune"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	2
"Quebec City"	"Google Men's Short Sleeve Hero Tee Light Blue"	1
"Quebec City"	"Google Men's Lightweight Microfleece Jacket Black"	1
"Quebec City"	"Google Women's Short Sleeve Hero Tee Sky Blue"	1
"Quebec City"	"YouTube Custom Decals"	1
"Quebec City"	"Google Men's Short Sleeve Hero Tee Heather"	1
"Quebec City"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Quebec City"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Quezon City"	"Google Women's Short Sleeve Hero Tee Black"	1
"Quezon City"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"Quezon City"	"Android Twill Cap"	1
"Quezon City"	"Seat Pack Organizer"	1
"Quezon City"	"YouTube Men's Long Sleeve Pullover Badge Tee Heather"	1
"Quezon City"	"Google Men's 100% Cotton Short Sleeve Hero Tee Red"	1
"Quimper"	"Google Bluetooth Speaker-Power Bank"	1
"Raleigh"	"Google Onesie Royal Blue"	1
"Redmond"	"YouTube RFID Journal"	2
"Redwood City"	"Google Women's Short Sleeve Hero Tee White"	1
"Redwood City"	"Google Women's Convertible Vest-Jacket Black"	1
"Redwood City"	"Google Bluetooth Speaker-Power Bank"	1
"Redwood City"	"Google Lunch Bag"	1
"Redwood City"	"Nest® Cam Indoor Security Camera - USA"	1
"Redwood City"	"Google Men's Convertible Vest-Jacket Pewter"	1
"Redwood City"	"Google Men's Vintage Badge Tee Sage"	1
"Rexburg"	"24 oz YouTube Sergeant Stripe Bottle"	1
"Rexburg"	"Google 17oz Stainless Steel Sport Bottle"	1
"Rexburg"	"Android 17oz Stainless Steel Sport Bottle"	1
"Richardson"	"Micro Wireless Earbud"	1
"Rio de Janeiro"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Rio de Janeiro"	"Softsided Travel Pouch Set"	1
"Rio de Janeiro"	"Google Blackout Cap"	1
"Rio de Janeiro"	"Google Men's  Zip Hoodie"	1
"Rio de Janeiro"	"Google Infuser-Top Water Bottle"	1
"Riyadh"	"Google Youth Short Sleeve T-shirt Yellow"	1
"Riyadh"	"Google 3/4 Sleeve Raglan Badge Henley Black"	1
"Riyadh"	"Red Spiral Google Notebook"	1
"Riyadh"	"Leatherette Journal"	1
"Rome"	"YouTube Leatherette Notebook Combo"	1
"Rome"	"Keyboard DOT Sticker"	1
"Rome"	"Google RFID Journal"	1
"Rome"	"Google Women's 3/4 Sleeve Baseball Raglan Heather/Black"	1
"Rome"	"Leatherette Journal"	1
"Rosario"	"Google Men's Vintage Badge Tee Black"	1
"Rotterdam"	"YouTube Wool Heather Cap Heather/Black"	1
"Sacramento"	"Google Blackout Cap"	1
"Saint Petersburg"	"Google Men's Quilted Insulated Vest Black"	1
"Saint Petersburg"	"Google Women's Lightweight Microfleece Jacket"	1
"Saint Petersburg"	"YouTube Hard Cover Journal"	1
"Saint Petersburg"	"Leatherette Journal"	1
"Sakai"	"YouTube Leatherette Notebook Combo"	1
"Salem"	"Google 17oz Stainless Steel Sport Bottle"	2
"Salem"	"Google Bib White"	2
"Salem"	"Google Long Sleeve Raglan Badge Henley Ocean Blue"	2
"Salem"	"Android 24 oz Contigo Bottle"	2
"Salford"	"Google Car Clip Phone Holder"	1
"San Antonio"	"Android 17oz Stainless Steel Sport Bottle"	2
"San Bruno"	"Google Water Resistant Bluetooth Speaker"	2
"San Bruno"	"22 oz YouTube Bottle Infuser"	2
"San Bruno"	"Android Glass Water Bottle with Black Sleeve"	2
"San Bruno"	"YouTube RFID Journal"	2
"San Bruno"	"Foam Can and Bottle Cooler"	2
"San Bruno"	"YouTube Trucker Hat"	2
"San Diego"	"Google Zipper-front Sports Bag"	2
"San Diego"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	2
"San Francisco"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	12
"San Jose"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	8
"San Mateo"	"YouTube Trucker Hat"	1
"San Mateo"	"Google Device Holder Sticky Pad"	1
"San Mateo"	"Google Tri-blend Hoodie Grey"	1
"San Salvador"	"Google Device Holder Sticky Pad"	1
"Sandton"	"Google Water Resistant Bluetooth Speaker"	1
"Santa Clara"	"Google Women's Short Sleeve Hero Tee White"	2
"Santa Clara"	"Google Trucker Hat"	2
"Santa Clara"	"Nest® Cam Indoor Security Camera - USA"	2
"Santa Clara"	"Nest® Protect Smoke + CO Black Wired Alarm-USA"	2
"Santa Clara"	"Google Men's Quilted Insulated Vest Black"	2
"Santa Fe"	"SPF-15 Slim & Slender Lip Balm"	1
"Santa Fe"	"Google Phone Sanitizer"	1
"Santa Fe"	"Google Water Resistant Bluetooth Speaker"	1
"Santa Monica"	"Google Slim Utility Travel Bag"	1
"Santiago"	"1 oz Hand Sanitizer"	1
"Santiago"	"Google Women's Short Sleeve V-Neck Tee Black"	1
"Santiago"	"Sport Bag"	1
"Santiago"	"Google Kick Ball"	1
"Santiago"	"YouTube Men's 3/4 Sleeve Henley"	1
"Santiago"	"Micro Wireless Earbud"	1
"Santiago"	"Leather and Metal Ballpoint Pen"	1
"Sao Paulo"	"22 oz Mini Mountain Bottle"	1
"Sao Paulo"	"Android Sticker Sheet Ultra Removable"	1
"Sao Paulo"	"Google Men's Short Sleeve Performance Badge Tee Charcoal"	1
"Sao Paulo"	"8 pc Android Sticker Sheet"	1
"Sao Paulo"	"22 oz YouTube Bottle Infuser"	1
"Sao Paulo"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Sao Paulo"	"Google Slim Utility Travel Bag"	1
"Sao Paulo"	"Android Lunch Kit"	1
"Sao Paulo"	"Waze Mood Ninja Window Decal"	1
"Sao Paulo"	"YouTube Custom Decals"	1
"Sao Paulo"	"Compact Eco Journal"	1
"Sao Paulo"	"YouTube Hard Cover Journal"	1
"Sao Paulo"	"Sport Bag"	1
"Sao Paulo"	"Google Men's Convertible Vest-Jacket Pewter"	1
"Sao Paulo"	"Android Women's Short Sleeve Hero Tee Black"	1
"Sao Paulo"	"Google Men's  Zip Hoodie"	1
"Sao Paulo"	"Red Shine 15 oz Mug"	1
"Sao Paulo"	"Google Heavyweight Long Sleeve Hero Tee Navy"	1
"Sao Paulo"	"Google Men's Vintage Badge Tee Black"	1
"Sao Paulo"	"Color Changing Grip Pen"	1
"Sao Paulo"	"Set of 3 Packing Cubes"	1
"Sao Paulo"	"Red Spiral Google Notebook"	1
"Sao Paulo"	"YouTube Men's Short Sleeve Hero Tee White"	1
"Sao Paulo"	"Softsided Travel Pouch Set"	1
"Sao Paulo"	"7&quot; Dog Frisbee"	1
"Sao Paulo"	"Suitcase Organizer Cubes"	1
"Sao Paulo"	"Google Infuser-Top Water Bottle"	1
"Sao Paulo"	"1 oz Hand Sanitizer"	1
"Sao Paulo"	"Google Collapsible Pet Bowl"	1
"Sao Paulo"	"Google Men's Skater Tee Charcoal"	1
"Sao Paulo"	"Google Men's Microfiber 1/4 Zip Pullover Grey/Black"	1
"Sao Paulo"	"Google Water Resistant Bluetooth Speaker"	1
"Sao Paulo"	"ATOM Wireless Earbud Headset"	1
"Sao Paulo"	"Aria Bluetooth Speaker"	1
"Sao Paulo"	"Google Women's Short Sleeve Hero Tee Red Heather"	1
"Sapporo"	"Android Toddler Short Sleeve T-shirt Pink"	1
"Seattle"	"Google Laptop and Cell Phone Stickers"	4
"Seattle"	"Android Men's  Zip Hoodie"	4
"Seoul"	"24 oz YouTube Sergeant Stripe Bottle"	1
"Seoul"	"YouTube Custom Decals"	1
"Seoul"	"Google Flashlight"	1
"Seoul"	"Android Men's Long Sleeve Badge Crew Tee Heather"	1
"Seoul"	"YouTube Leatherette Notebook Combo"	1
"Seoul"	"Google Men's Long Sleeve Raglan Ocean Blue"	1
"Seoul"	"Waze Baby on Board Window Decal"	1
"Seoul"	"Google Vintage Henley Grey/Black"	1
"Seoul"	"Red Shine 15 oz Mug"	1
"Seoul"	"Google 17oz Stainless Steel Sport Bottle"	1
"Seoul"	"Google Onesie Green"	1
"Seoul"	"Google Men's Weatherblock Shell Jacket Black"	1
"Seoul"	"Android Men's Vintage Henley"	1
"Seoul"	"Windup Android"	1
"Seoul"	"Ballpoint Stylus Pen"	1
"Seoul"	"Ballpoint LED Light Pen"	1
"Seoul"	"22 oz YouTube Bottle Infuser"	1
"Seoul"	"Waze Dress Socks"	1
"Seoul"	"Android Journal Book Set"	1
"Seoul"	"Compact Journal with Recycled Pages"	1
"Seoul"	"Google Men's Short Sleeve Performance Badge Tee Pewter"	1
"Seoul"	"Android Men's  Zip Hoodie"	1
"Seoul"	"Sport Bag"	1
"Seoul"	"Compact Selfie Stick"	1
"Seoul"	"Fashion Sunglasses & Pouch"	1
"Sherbrooke"	"Nest® Cam Indoor Security Camera - USA"	1
"Sherbrooke"	"Google Men's Long Sleeve Raglan Ocean Blue"	1
"Sherbrooke"	"Google Blackout Cap"	1
"Shibuya"	"Google Men's Vintage Badge Tee Sage"	1
"Shibuya"	"YouTube Men's Short Sleeve Hero Tee White"	1
"Shinjuku"	"Google Trucker Hat"	1
"Shinjuku"	"Large Zipper Top Tote Bag"	1
"Shinjuku"	"Plastic Sliding Flashlight"	1
"Shinjuku"	"YouTube Men's Vintage Tee"	1
"Shinjuku"	"Windup Android"	1
"Shinjuku"	"Colored Pencil Set"	1
"Shinjuku"	"Google 17oz Stainless Steel Sport Bottle"	1
"Shinjuku"	"Google Women's Scoop Neck Tee White"	1
"Singapore"	"Google Alpine Style Backpack"	2
"Singapore"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	2
"Skopje"	"Color Changing Grip Pen"	1
"South El Monte"	"Google Men's Vintage Badge Tee White"	1
"South San Francisco"	"Google Men's Short Sleeve Hero Tee Heather"	1
"South San Francisco"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"South San Francisco"	"Android Sticker Sheet Ultra Removable"	1
"South San Francisco"	"Google Rucksack"	1
"St. John's"	"Google 2200mAh Micro Charger"	1
"St. Louis"	"Google Bib Red"	1
"St. Louis"	"Google Wool Heather Cap Heather/Navy"	1
"Stanford"	"Google Men's  Zip Hoodie"	1
"Stockholm"	"Google Men's Pullover Hoodie Grey"	1
"Stockholm"	"Metal Earbuds with Small Zipper Case"	1
"Stockholm"	"Android Rise 14 oz Mug"	1
"Stockholm"	"Waterproof Backpack"	1
"Stockholm"	"Google Women's Short Sleeve Shirt Blue"	1
"Stockholm"	"24 oz YouTube Sergeant Stripe Bottle"	1
"Stockholm"	"Google Bluetooth Headphones"	1
"Stockholm"	"Metal Texture Roller Pen"	1
"Stockholm"	"Crunch Noise Dog Toy"	1
"Stockholm"	"Ballpoint Stylus Pen"	1
"Stockholm"	"Google Men's 100% Cotton Short Sleeve Hero Tee Black"	1
"Stockholm"	"Google  Women's Muscle Tee"	1
"Stockholm"	"Google Women's Short Sleeve Badge Tee Red Heather"	1
"Stockholm"	"YouTube Men's Short Sleeve Hero Tee White"	1
"Stockholm"	"Google Men's Short Sleeve Hero Tee Heather"	1
"Stockholm"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Stockholm"	"Google French Terry Cap"	1
"Stockholm"	"Google Women's Short Sleeve Hero Tee Red Heather"	1
"Stockholm"	"Google Heavyweight Long Sleeve Hero Tee Navy"	1
"Stockholm"	"Google Water Resistant Bluetooth Speaker"	1
"Stuttgart"	"Google Pocket Bluetooth Speaker"	1
"Sunnyvale"	"Nest® Cam Outdoor Security Camera - USA"	6
"Sunnyvale"	"Nest® Cam Indoor Security Camera - USA"	6
"Sydney"	"YouTube Trucker Hat"	3
"Sydney"	"Basecamp Explorer Powerbank Flashlight"	3
"Taguig"	"Google Men's Vintage Badge Tee Black"	1
"Taguig"	"Google Men's Convertible Vest-Jacket Pewter"	1
"Taguig"	"Metal Texture Roller Pen"	1
"Taguig"	"Google Men's Watershed Full Zip Hoodie Grey"	1
"Taguig"	"Google 5-Panel Cap"	1
"Taguig"	"Google Luggage Tag"	1
"Taguig"	"YouTube RFID Journal"	1
"Tampa"	"Android Wool Heather Cap Heather/Black"	1
"Tel Aviv-Yafo"	"YouTube Hard Cover Journal"	2
"Tel Aviv-Yafo"	"Nest® Protect Smoke + CO Black Wired Alarm-USA"	2
"Tel Aviv-Yafo"	"Android 17oz Stainless Steel Sport Bottle"	2
"Tel Aviv-Yafo"	"22 oz YouTube Bottle Infuser"	2
"Tempe"	"Google Car Clip Phone Holder"	1
"Tempe"	"Google Snapback Hat Black"	1
"Tempe"	"Android Men's Engineer Short Sleeve Tee Charcoal"	1
"Tempe"	"Windup Android"	1
"The Dalles"	"Google Laptop and Cell Phone Stickers"	1
"The Dalles"	"YouTube Men's Long Sleeve Pullover Badge Tee Heather"	1
"Thessaloniki"	"Red Spiral Google Notebook"	1
"Thessaloniki"	"Retractable Ballpoint Pen Red"	1
"Thessaloniki"	"Google Bib Red"	1
"Timisoara"	"Android BTTF Cosmos Graphic Tee"	1
"Timisoara"	"ATOM Wireless Earbud Headset"	1
"Timisoara"	"Galaxy Screen Cleaning Cloth"	1
"Toronto"	"YouTube Trucker Hat"	4
"University Park"	"Waterproof Backpack"	1
"Vancouver"	"YouTube Men's Short Sleeve Hero Tee Black"	1
"Vancouver"	"Google Device Stand"	1
"Vancouver"	"Google Women's Fleece Hoodie"	1
"Vancouver"	"Micro Wireless Earbud"	1
"Vancouver"	"Google Bluetooth Speaker-Power Bank"	1
"Vancouver"	"Google Laptop Backpack"	1
"Vancouver"	"YouTube Hard Cover Journal"	1
"Vienna"	"Google Men's 100% Cotton Short Sleeve Hero Tee Red"	1
"Vienna"	"Google Men's  Zip Hoodie"	1
"Vienna"	"Android Wool Heather Cap Heather/Black"	1
"Vienna"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	1
"Vienna"	"YouTube Notebook and Pen Set"	1
"Villeneuve-d'Ascq"	"Google Snapback Hat Black"	1
"Vilnius"	"Google Men's 100% Cotton Short Sleeve Hero Tee Red"	1
"Vladivostok"	"Google Canvas Tote Natural/Navy"	1
"Warsaw"	"Keyboard DOT Sticker"	2
"Warsaw"	"Google Laptop and Cell Phone Stickers"	2
"Washington"	"Google Insulated Stainless Steel Bottle"	2
"Washington"	"Google Laptop Backpack"	2
"Washington"	"YouTube Men's 3/4 Sleeve Henley"	2
"Washington"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	2
"Washington"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	2
"Washington"	"Google G Noise-reducing Bluetooth Headphones"	2
"Waterloo"	"Google Long Sleeve Raglan Badge Henley Ocean Blue"	1
"Watford"	"YouTube Luggage Tag"	1
"Wellesley"	"Colored Pencil Set"	1
"Westlake Village"	"Google Men's Long Sleeve Raglan Ocean Blue"	1
"Westville"	"Sport Bag"	1
"Wrexham"	"Google Laptop and Cell Phone Stickers"	1
"Wroclaw"	"Google Bluetooth Speaker-Power Bank"	1
"Yokohama"	"Google Men's Pullover Hoodie"	2
"Zagreb"	"Google Men's Watershed Full Zip Hoodie Grey"	2
"Zhongli District"	"Google Men's 100% Cotton Short Sleeve Hero Tee White"	2
"Zurich"	"Sport Bag"	2
"Zurich"	"Google Men's Vintage Badge Tee Black"	2



**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

SELECT DISTINCT "city", SUM("totalTransactionRevenue") FROM all_sessions
GROUP BY "city"
ORDER BY SUM("totalTransactionRevenue") DESC

SELECT DISTINCT "country", SUM("totalTransactionRevenue") FROM all_sessions
GROUP BY "country"
ORDER BY SUM("totalTransactionRevenue") DESC



Answer: Just looking at where the most revenue comes from we see that nearly all of it
is from the US. This most likely means we are dealing with an American based business
who markets towards Americans with some fringe order from elsewhere. On the topic of 
cities we see that there is a lot of missing information as to where in America the
orders are coming from but there is a sizeable market in Mountain View.







