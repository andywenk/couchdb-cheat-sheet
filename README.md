# CouchDB-Cheat-Sheet

This is a cheat-sheet for CouchDB's RESTful API. It's showing the most common and used request commands and shows
an example for each part. 

The motivation for making this cheat-sheet was actually pushed at my job at SinnerSchrader because it was one of my 
goals for this year. But having written the first German CouchDB book [1] - besides Mario Scheliga's "CouchDB kurz & gut" [2] - I
also thought it is a good thing to have. And therefor, this first version is only available in German language. But hey, 
I will provide a english version as soon as I can find some time. Or - don't you wanna fork and translate it? 

## First Version

The first version of this cheat-sheet covers the database- and document-part of the API. The next step will be to include
the _design document-part and views. 

## Is this really a cheat-sheet?

I would say yes! Some of you may think no. But here is the reason why this paper is written as it is. Can you give me 
five really good documentation ressources (not speaking of books) for CouchDB? Ok let's say two. Yay! The official CouchDB 
Wiki [3] and the API docu at couchbase.org [4]. They are both kind of ... ok. What is really missing for CouchDB are 
examples, examples, examples. I tried to close this gap by providing an exmaple for each request with the full result (at 
least once). So that's the reason, why it feels like a book, but is still meant as a cheat-sheet.

## Are there other cheat-sheets for CouchDB?

No. Ahm ... yes - but they suck because they are all useless. There is only one I like. It's Jan-Piet Mens 
"The Antepenultimate CouchDB Reference Card (cheat sheet)" [5]. It provides a good overview. Give it a glimps. 

## How to build the cheat-sheet?

Oh - that's a simple one. I know you have Tex / LaTex installed (no? go and fight with fuckin' word!). So for 
compiling a PDF Version, simply run

> pdflatex couchdb-cheat-sheet.tex

The result will be a file called couchdb-cheat-sheet.pdf. Ok - for the ones without Tex, simply download the included 
PDF in this repository ;-)

## What's next?

_ translate it to english  
_ extend it with _design and views  

## What I ask you for!

This is work in progress. I really encourage you to fork this repo and extend it by yourself. Or do you have any 
suggestions? Write me an email to andy@nms.de or use the issue tracker here. Thank you! 

[1] http://www.galileocomputing.de/katalog/buecher/titel/gp/titelID-2462  
[2] http://www.oreilly.de/catalog/couchdbprger/  
[3] http://wiki.apache.org/couchdb/  
[4] http://www.couchbase.org/sites/default/files/uploads/all/documentation/couchbase-api.html  
[5] http://nosql.mypopescu.com/post/721887139/couchdb-cheat-sheet  
