# PBI_NYT_Spelling_Bee
Using Power BI to crack the NYT Spelling Bee game

Hello! 

This document will outline my thought process and some high-level data modeling / DAX strategy used to "crack" the NYT Spelling Bee Game using a Power BI Dashboard, but first, some background: 

My wife and I battle for the quickest time on the NYT daily mini crossword amoungst a small group of friends. It is a great time but my wife usually wins. The other day she showed me the Spelling Bee game that is also part of the NYT Games app (along with Wordle). The Spelling Bee game is much like Scrabble, but you MUST use the designated character and you are able to re-use any of the characters as needed. As she was trying to come up with words she had yet to use for that day's puzzle (to reach "genius" status) I began to think about how I could crack this game to reach "genius" status before her, just to flex. I could easily find a website that posted the answers but that felt a little cheap, so I decided to try my hand at building my own solution because in my mind this was fair game...

Information I Needed: 
  1. List of possible words
  2. Length of said words
  3. Count of distinct characters within said words
  4. The letters used in said words (i.e. APPLE - {A,P,L,E}
  5. Possible Letters (the alphabet)


1. List of possible words: 
I knew the rules were similar to Scrabble, so a Scrabble dictionary was the best place to start. There are some obscure Scrabble words that the Spelling Bee game does not recognize so if anything this list would be overkill. Luckly some righteous souls on Stack Overflow posted a list of words from the 2019 version of the Collins Scrabble Dictionary in .txt format. All I had to do was dump it into an .xls file and I was off to the races. 

2. Length of said words: 
Importing the word data into PBI allowed me to start manipulating and cleaning things. Adding a column that counted the word length and filtered out words that were < 4 characters is simple enough. 

3+4. Count of distinct characters within said words + The letters used in said words
This is where it gets fun and logic really sets in. I need a way

5. Possible Letters (the alphabet)


