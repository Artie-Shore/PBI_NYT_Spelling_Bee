# PBI_NYT_Spelling_Bee
Using Power BI to "crack" the New York Times Spelling Bee game

Hello! 

This document outlines my thought process and some high-level data modeling / DAX strategy used to "crack" the NYT Spelling Bee game using the magic of Power BI. But first, some background: 

My wife and I battle for the quickest time on the NYT Daily Mini Crossword amongst a small group of friends. I enjoy it but my wife usually wins. The other day she showed me the Spelling Bee game that is also part of the NYT Games app (along with Wordle). The Spelling Bee game is much like Scrabble, but you must use the designated character and you are free to re-use any of the characters as needed. As she was trying to come up with words she had yet to use for that day's puzzle (to reach "genius" status) I began to think about how I could crack this game to reach genius status before her and finally get a win. I could likely find a website that posted the answers but that felt a little cheap, so I decided to try my hand at building my own solution because in my mind that made it fair...


High level logic: 
If I have a list of all possible words, I could filter it to show words that have the required letter and were comprised of some combination of the remaining 6 letters. I wanted it to work based on user input and update dynamically, and having spent a few years building Power BI dashboards and playing with slicers I figured I could accomplish this in Power BI with relative ease. If I had a list of all possible words, the distinct letters that make up those words, and 7 "letter selection" tables to reference, I could use DAX to create a measure formula that calculates whether a word meets the criteria and then filter from there. 

Information I needed: 
1. List of possible words
2. Length of said words
3. Count of distinct characters within said words
4. The distinct letters used in said words (i.e. APPLE = {A,P,L,E})
5. Possible Letters (the alphabet)
6. DAX Formula(s)


List of possible words: I knew the rules were similar to Scrabble, so a Scrabble dictionary was the best place to start. There are some obscure Scrabble words that the Spelling Bee game does not recognize so if anything this list would be overkill. Luckily some righteous souls on Stack Overflow posted a list of words from the 2019 version of the Collins Scrabble Dictionary in .txt format. All I had to do was dump it into an .xls file. 


Length of said words + Count of distinct characters within said words + The distinct letters used in said words: This is where it gets fun. Importing the word data into PBI allowed me to start manipulating with Power Querey. Adding a column that counted the word length and filtered out words that were < 4 characters was a good start. I needed a way to count distinct characters for each word in the list, so I created a custom column to copy the word, then split this custom column by position into new rows. So for the example APPLE, I would get 5 rows that looked like the following: 

Example 1:
  1. APPLE, A
  2. APPLE, P
  3. APPLE, P
  4. APPLE, L
  5. APPLE, E

Nice, but I wanted distinct characters. I went ahead and removed duplicates (so there is only 1 "APPLE, P" row): 

Example 2:
  1. APPLE, A
  2. APPLE, P
  3. APPLE, L
  4. APPLE, E

This was the foundation I needed. I used this to generate a table of the unique words with their respective length and distinct character count, and created a similar table of words with every unique character that is part of that word (like Example 2 above). Creating two tables provided me with a parent table named "Words_Header" (unique words that act as a unique IDs) and a child table named "Words" (the word or "ID" along with one of its disctinct letters for every distinct letter in the word, like Example 2 above).


Possible Letters: I knew I needed to select 7 letters and I want to have each in its own individual slicer, so I went ahead and created an alphabet table in the query editor and duplicated it until I had 7 alphabet tables (tables Letters_1 through Letters_7).  

Once all the data was loaded I created a relationship to link the 2 word tables based on the word column (or, IDs as I referred to them earlier). To identify matching words I needed a DAX measure to filter words out of visuals based on the selected values of alphabet tables 1-7. 


The DAX formula(s): I started by creating variables that calculate the number of instances a chosen letter occurs in a parent word by counting the rows in its corresponding child table that match the selected value of the relavent alphabet table (example below). 

Example 1: 
var has_1 = CALCULATE(COUNTROWS(Words), FILTER(Words, Words[Words_Split] = SELECTEDVALUE(Letters_1[Letter])))

Since I removed duplicate letters for each word in the child word table, the value of the variable above for each word could only ever result in either 0 (the word does not have the selected letter) or 1 (the word has the letter). I created this type of variable for letter tables 1 through 7. Summing the values of variables has_1 through has_7 into a new variable called all_letters let me know how many of my selected letters are present in the word. If all_letters was equal to 7, then all 7 selected letters were present. From there I could filter on variable has_1 to determine which words contain the required "golden" letter. 

This is great, but its not enough to know if the selected letters are present because I also need to exlude words that have letters that are not selected in has_1 through has_7. Going back to the APPLE Example, if I select A, P, and E, but not L, I don't want APPLE to show up as a possible match. Luckily, accounting for this is fairly straightforward. I knew that the variable all_letters provides the count of selected distinct letters in a given word. I also knew that words comprised exclusively of combinations of the selected letters could only ever have a distinct character count equal to the value of the all_letters variable, because if the distinct character count for a word is > all_letters, that would imply that there MUST be some other letter(s) in the word that I didn't count. Using the a few other words that stem from the reliable APPLE example to explain:

Example: [Word], [Length], [Distinct Characters], [Selected Letters Used]

Selected Letters: A,P,E

  1. APPLE, 5, 4, 3
  2. APE, 3, 3, 3
  3. PEA, 3, 3, 3


Adding a variable to calculate the distinct letters for each word and forming an IF statement based on all of this logic produced the "Show" measure that dictates whether a word in the parent table is a potential match. The "Show" measure was then combined with the SUMMARIZE function to produce a count based on the parent word table, and from there it was visuals, visuals, visuals.  

Enjoy! 
