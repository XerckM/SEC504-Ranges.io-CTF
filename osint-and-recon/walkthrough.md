## Website Reconnaissance 
### What is ISS Employee Jerald Orestes' job title?
1. Visited https://www.issplaylist.com/
2. Clicked Company on the navigation bar
3. Located Jerald Orestes employee name by hitting ctrl+f; typed Jerald; and hit search
4. Web Developer

## Username Format Reconnaissance
### What convention does ISS Playlist use for username creation?
1. While in the Company page, noticed that the employee names are clickable when I hover the mouse over
2. After hovering over a few names I found the convention they used for their usernames
3. First Initial, Last Name

## ClippedBin OSINT 1
### Examine the website contents at www.clippedbin.com. Submit the flag.
1. I opened the link and is brought to create page and didn't find the flag.
2. I examined the other navigation links and noticed the search bar on Recent and Trending
3. I tested the search function by typing basic words connected to issplaylist.com and understood that it shows results based on the keyword you typed in the search bar.
4. I figured that I needed the the flag format NetWars{FLAG} and the easiest way to find this flag is by searching NetWars
5. Clicked the first link that says Jackets Play Hands
6. NetWars{ImportantOSINT}

## ClippedBin OSINT 2
### Use the www.clippedbin.com website to identify ISS Playlist password information disclosed by an attacker. There's a password repeated 4 times in the list. Submit the password as the answer.
1. Typed 'passwords' in the search bar
2. The answer is in the only link
3. spacestation

## IP Camera Logon
### Access the cam.issplaylist.com camera logon page. Submit the flag.
1. Accessed the link and the flag is at the bottom banner
2. NetWars{WebcamServer}

## IP Camera Access
### Gain access to the IP camera system through the web interface logon page. Submit the flag displayed after logging in to the system.
1. It took me a while before I realized how to answer this question. I realized the whole section is about OSINT
2. I used ClippedBin and typed 'cam.issplaylist.com'
3. The link showed the credentials website credentials
4. username: admin, password: admin
5. NetWars{WebcamLoggedInAccess}

## IP Camera Disclosure
### Who does Bridget need to follow up with?
1. Used the camera controls to find a clue
2. The answer is on the whiteboard
3. Phyliss
