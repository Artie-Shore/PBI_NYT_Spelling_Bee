# PBI_NYT_Spelling_Bee
Using Power BI to crack the NYT Spelling Bee game

Hello! 

This document outlines my thought process and some high-level data modeling / DAX strategy used to "crack" the NYT Spelling Bee Game using a Power BI, but first, some background: 

My wife and I battle for the quickest time on the NYT daily mini crossword amoungst a small group of friends. It is a great time but my wife usually wins. The other day she showed me the Spelling Bee game that is also part of the NYT Games app (along with Wordle). The Spelling Bee game is much like Scrabble, but you MUST use the designated character and you are free to re-use any of the characters as needed. As she was trying to come up with words she had yet to use for that day's puzzle (to reach "genius" status) I began to think about how I could crack this game to reach "genius" status before her, just to flex. I could easily find a website that posted the answers but that felt a little cheap, so I decided to try my hand at building my own solution because in my mind that made it fair...


High Level logic: 
If I have a list of all possible words, I could filter it to show words that MUST have the required letter and were comprised of some combination of the remaining 6 letters. I wanted it to work based on user input and update dynamically (as opposed to going in and editing the any logic each time). If I had a list of words, the distinct letters that make up those words, and 7 sperate "letter selection" tables to reference, I could use DAX to create a measure that calcualtes whether a word meets the criteria. 

Information I Needed: 
1. List of possible words
2. Length of said words
3. Count of distinct characters within said words
4. The distinct letters used in said words (i.e. APPLE - {A,P,L,E}
5. Possible Letters (the alphabet)
6. DAX?


List of possible words: I knew the rules were similar to Scrabble, so a Scrabble dictionary was the best place to start. There are some obscure Scrabble words that the Spelling Bee game does not recognize so if anything this list would be overkill. Luckly some righteous souls on Stack Overflow posted a list of words from the 2019 version of the Collins Scrabble Dictionary in .txt format. All I had to do was dump it into an .xls file and I was off to the races. 


Length of said words + Count of distinct characters within said words + The distinct letters used in said words: 
This is where it gets fun. Importing the word data into PBI allowed me to start manipulating and cleaning things using M language. Adding a column that counted the word length and filtered out words that were < 4 characters was a good start. I needed a way to count distinct characters for each word in the list, ao to do this I created a custom column to copy the word, then split this custom column by position into new rows. So for the example APPLE, I would get 5 rows that look like the following: 

Example 1:
  1. APPLE, A
  2. APPLE, P
  3. APPLE, P
  4. APPLE, L
  5. APPLE, E

Nice, but I want distinct characters. I went ahead and removed duplicates (so there is only 1 "APPLE, P" row): 

Example 2:
  1. APPLE, A
  2. APPLE, P
  3. APPLE, L
  4. APPLE, E

Amazing. This is the foundation I needed. I used this to generate a table of just the unique words with their respective length and distinct character count, and another table of words repeated for each time a character was used along with the unique character that is part of that word (Example 2 above). This essentially provided me with a parent table (unique words that act as a unique IDs) and a child table (one of the disctinct letters and the corresponding word, where each word acts as a unique ID).


Possible Letters (the alphabet): I know I need to select 7 letters and I want to have each in a slicer, so I went ahead and created an alphabet table in the query editor and duplicated it until I had 7 alphabet tables. 

Once all the data was loaded in I created a relationship to link the 2 word tables based on the word column (or, IDs as I referred to them earlier). To identify matching words I needed a DAX measure to filter words out of visuals based on the selected values of alphabet tables 1-7. 


The DAX: I started by creating variables that calculate the number of instances a chosen letter occurs in a parent word by counting the rows in its corresponding child table that match the selected value of each of the alphabet tables (example below). 

Example 1: 
has_1 = CALCULATE(COUNTROWS(Words), FILTER(Words, Words[Words_Split] = SELECTEDVALUE(Letters_1[Letter])))

Since I removed duplicate letters for each word in the child word table, the value of the variable above for each word could only ever result in either 0 (the word does not have the selected letter) or 1 (the word has the letter!). I created a variable like this for letter tables 1-7, and then summing the values of each of those variables (into a new variable called all_letters) let me know how many of my selected letters are present in the word. If the sum of those variables (all_letters) is 7, then all 7 selected letters are present. 

This is great, but its not enough to know if the selected letters are present because we need to exlude words that have letters that are not selected. Going back to the APPLE Example, if I select A, P, and E, but not L, I obviously don't want APPLE to show up. This sounds more difficult than it actually is... I know that the variable all_letters provides the count of selected letters in a given word. I also know that words comprised exclusively of combinations of the selected letters could only ever have a distinct character count equal the value of the all letters variable, because if the distinct character count for a word is > all_letters, there MUST be some letter that we have not selected in the word. Adding a quick variable to calculate the distinct letters for the word and forming an if statement based on this logic produced the "show" measure that dictates whether a word in the parent table meets the criteria. 

The "show" measure was then combined with summarize to produce a count based on the parent word table. 

Enjoy! 






