# PBI_NYT_Spelling_Bee
Using Power BI to crack the NYT Spelling Bee game

Hello! 

This document outlines my thought process and some high-level data modeling / DAX strategy used to "crack" the NYT Spelling Bee Game using a Power BI, but first, some background: 

My wife and I battle for the quickest time on the NYT daily mini crossword amoungst a small group of friends. It is a great time but my wife usually wins. The other day she showed me the Spelling Bee game that is also part of the NYT Games app (along with Wordle). The Spelling Bee game is much like Scrabble, but you MUST use the designated character and you are free to re-use any of the characters as needed. As she was trying to come up with words she had yet to use for that day's puzzle (to reach "genius" status) I began to think about how I could crack this game to reach "genius" status before her, just to flex. I could easily find a website that posted the answers but that felt a little cheap, so I decided to try my hand at building my own solution because in my mind that made it fair...

High Level logic: 
If I have a list of all possible words, I could filter it to show words that MUST have the required letter and were comprised of some combination of the remaining 6 letters. I wanted it to work based on user input and update dynamically (as opposed to going in and editing the any M logic each time) so I could use it daily. If I had a list of words, the distinct letters that make up those words, and 7 sperate "letter selection" ftilers, I could use DAX to create a measure that calcualtes whehter a word meets the criteria. 

Information I Needed: 
1. List of possible words
2. Length of said words
3. Count of distinct characters within said words
4. The distinct letters used in said words (i.e. APPLE - {A,P,L,E}
5. Possible Letters (the alphabet)


List of possible words: I knew the rules were similar to Scrabble, so a Scrabble dictionary was the best place to start. There are some obscure Scrabble words that the Spelling Bee game does not recognize so if anything this list would be overkill. Luckly some righteous souls on Stack Overflow posted a list of words from the 2019 version of the Collins Scrabble Dictionary in .txt format. All I had to do was dump it into an .xls file and I was off to the races. 

Length of said words + Count of distinct characters within said words + The distinct letters used in said words: 
This is where it gets fun and logic really sets in. Importing the word data into PBI allowed me to start manipulating and cleaning things using M language. Adding a column that counted the word length and filtered out words that were < 4 characters was a good start. I needed a way to count distinct characters for each word in the list, ao to do this I created a custom column to copy the word, then split this custom column by position into new rows. So for the example APPLE, I would get 5 rows that look like the following: 

  1. APPLE, A
  2. APPLE, P
  3. APPLE, P
  4. APPLE, L
  5. APPLE, E

Nice, but I want distinct characters. I went ahead and removed duplicates (so there is only 1 "APPLE, P" row): 

  1. APPLE, A
  2. APPLE, P
  3. APPLE, L
  4. APPLE, E

Amazing. This is the foundation for the data. I like working with relationsh

5. Possible Letters (the alphabet)


