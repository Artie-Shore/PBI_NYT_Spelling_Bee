# PBI_NYT_Spelling_Bee
Using Power BI to crack the NYT Spelling Bee game

Hello! 

This document outlines my thought process and some high-level data modeling / DAX strategy used to "crack" the NYT Spelling Bee Game using a Power BI, but first, some background: 

My wife and I battle for the quickest time on the NYT daily mini crossword amoungst a small group of friends. It is a great time but my wife usually wins. The other day she showed me the Spelling Bee game that is also part of the NYT Games app (along with Wordle). The Spelling Bee game is much like Scrabble, but you MUST use the designated character and you are able to re-use any of the characters as needed. As she was trying to come up with words she had yet to use for that day's puzzle (to reach "genius" status) I began to think about how I could crack this game to reach "genius" status before her, just to flex. I could easily find a website that posted the answers but that felt a little cheap, so I decided to try my hand at building my own solution because in my mind this was fair game...

High Level View: 
There are 7 possible letters to choose from, and once must be 

Information I Needed: 
  -List of possible words
  -Length of said words
  -Count of distinct characters within said words
  -The distinct letters used in said words (i.e. APPLE - {A,P,L,E}
  -Possible Letters (the alphabet)


List of possible words: 
  I knew the rules were similar to Scrabble, so a Scrabble dictionary was the best place to start. There are some obscure Scrabble words that the Spelling Bee game does not recognize so if anything this list would be overkill. Luckly some righteous souls on Stack Overflow posted a list of words from the 2019 version of the Collins Scrabble Dictionary in .txt format. All I had to do was dump it into an .xls file and I was off to the races. 

Length of said words + Count of distinct characters within said words + The distinct letters used in said words
  This is where it gets fun and logic really sets in. Importing the word data into PBI allowed me to start manipulating and cleaning things using M language. Adding a column that counted the word length and filtered out words that were < 4 characters was a good start. I needed a way to count distinct characters for each word in the list, ao to do this I created a custom column to copy the word, then split this custom column by position into new rows. So for the example APPLE, I would get 5 rows that look like the following: 

  APPLE, A
  APPLE, P
  APPLE, P
  APPLE, L
  APPLE, E

Nice, but I want distinct characters. I went ahead and removed duplicates (so there is only 1 "APPLE, P" row): 

  APPLE, A
  APPLE, P
  APPLE, L
  APPLE, E

Amazing. This is the foundation for the data. I like working with relationsh

5. Possible Letters (the alphabet)


