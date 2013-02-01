% CouchDB-Cheat-Sheet
% Andreas Wenk – Version: 0.2.0 – August 2011
% 

910

Dieses Cheat-Sheet ist eine Übersicht der RESTful API von CouchDB
\cite{1}. Das Ziel ist es, die gebräuchlichsten Requests jeweils anhand
eines Beispiels zu zeigen. Unter \cite{2} ist eine CouchDB zu finden, in
der die Beispiele nachgestellt werden können (Basic-Auth: ohne Daten
‘ok’ klicken).

Unter \cite{3} ist die API-Referenz zu finden.

Damit die Listings so kurz als möglich sind, heisst die Datenbank, die
als Beispiel dienen soll ‘kina’. Die Grundstruktur der Dokumente in
dieser Datenbank ist folgendermaßen:

"~i~d": "fab911e6e9ed703bf536dccffe000937", "~r~ev":
"1-183f162a09532ccacf8223f6e2ba95f6", "lang": "javascript"

Alle Beispiel-Requests werden unter Verwendung von cURL \cite{10}
erstellt. Natürlich kann auch das von CouchDB mitgelieferte Webinterface
‘Futon’ genutzt werden. Allerdings rate ich davon ab, da bei der
Verwendung von cURL die Betrachtung der HTTP-Response zu wesentlich mehr
Verständnis führt.

Überblick
=========

CouchDB ist eine dokumentbasierte Datenbank und gehört zur Gruppe der
NoSQL Datenbanken. CouchDB ist ein Apache Open-Source Projekt und liegt
aktuell in der Version 1.1.0 vor. Einige wichtige Merkmale von CouchDB
sind:

-   in Erlang \cite{4} geschrieben

-   schemalos

-   API: HTTP Rest \cite{5}

-   implementiert ACID \cite{6} über MVCC \cite{7}

-   CAP Theorem \cite{8}:

    -   A (Availability) - ja

    -   P (Partition Tolerance) - ja

    -   C (Consistency) -\> Eventual Consistency

-   MapReduce \cite{9} für Views

-   Asynchrone Replikation in beide Richtungen - Master / Master

-   Revisions Management

Datenbanken
===========

Alle Datenbanken im Cluster GET \_all\_dbs
------------------------------------------

curl -X GET http://127.0.0.1:5984/~a~ll~d~bs

["~r~eplicator","~u~sers"]

Datenbank erstellen PUT
-----------------------

curl -X PUT http://127.0.0.1:5984/kina

"ok": true

Datenbankinformationen erhalten GET
-----------------------------------

curl -X GET http://127.0.0.1:5984/kina

"db~n~ame":"kina", "doc~c~ount":0, "doc~d~el~c~ount":0, "update~s~eq":0,
"purge~s~eq":0, "compact~r~unning":false, "disk~s~ize":79,
"instance~s~tart~t~ime":"1312577294992829", "disk~f~ormat~v~ersion":5,
"committed~u~pdate~s~eq":0

Datenbank löschen DELETE
------------------------

curl -X DELETE http://127.0.0.1:5984/kina

"ok": true

Datenbank replizieren POST \_replicate
--------------------------------------

(alte API) *(options)* create\_target:true, cancel:true, continuous:true

### lokal nach lokal

curl -X POST http://127.0.0.1:5984/~r~eplicate -H
"content-type:application/json" -d ’"source": "kina", "target": "anik",
"create~t~arget": true’

"ok":true, "session~i~d":"f75eb944bac70f40e77953f484afb64c",
"source~l~ast~s~eq":36, "history":
["session~i~d":"f75eb944bac70f40e77953f484afb64c", "start~t~ime":"Thu,
14 Apr 2011 20:36:12 GMT", "end~t~ime":"Thu, 14 Apr 2011 20:36:12 GMT",
"start~l~ast~s~eq":0, "end~l~ast~s~eq":36, "recorded~s~eq":36,
"missing~c~hecked":0, "missing~f~ound":14, "docs~r~ead":14,
"docs~w~ritten":14, "doc~w~rite~f~ailures":0 ]

### remote nach lokal (umgekehrt genauso)

*(!)* Bei der Nutzung von create\_target muss die Authentifizierung mit
einem User der Zieldatenbank erfolgen.

curl -X POST http://127.0.0.1:5984/~r~eplicate -H
"content-type:application/json" -d ’"source": "kina", "target":
"http://starsky:hutch@couchbuch.iriscouch.com/anik", "create~t~arget":
true’

"ok":true,"session~i~d":"ed5fa48d0a5ce9cee6941381754d796d",
"source~l~ast~s~eq":29,"replication~i~d~v~ersion":2, "history":
["session~i~d":"ed5fa48d0a5ce9cee6941381754d796d", "start~t~ime":"Wed,
31 Aug 2011 19:34:40 GMT", "end~t~ime":"Wed, 31 Aug 2011 19:34:41 GMT",
"start~l~ast~s~eq":0, "end~l~ast~s~eq":29,"recorded~s~eq":29,
"missing~c~hecked":0, "missing~f~ound":14, "docs~r~ead":14,
"docs~w~ritten":14, "doc~w~rite~f~ailures":0 ]

### remote nach remote

curl -X POST http://127.0.0.1:5984/~r~eplicate -H
"content-type:application/json" -d ’"source":
"http://andywenk.cloudant.com/kina", "target":
"http://starsky:hutch@couchbuch.iriscouch.com/kina", "create~t~arget":
true’

"ok":true,"no~c~hanges":true

Datenbank replizieren - filter
------------------------------

*(options)* filter:filter\_name,
query\_params:{filter\_name:query\_param},doc\_ids:
docid\_1,docid\_2,docid\_n

Filter werden in \_design Dokumenten erstellt. Ein Filter kann später
bei der Replikation genutzt werden, um nur Dokumente zu replizieren, die
den im Filter hinterlegten Kriterien entsprechen.

Filter erstellen:

curl -X PUT http://127.0.0.1:5984/kina/~d~esign/default -H
"content-type: application/json" -d ’"filters": "lang": "function(doc,
req) return javascript" == doc.lang " ’

Filter anwenden (davon ausgehend, es gibt drei Dokumente mit lang =
javascript):

curl -X POST http://127.0.0.1:5984/~r~eplicate -H
"content-type:application/json" -d ’"source": "kina", "target": "anik",
"create~t~arget": true, "filter": "default/lang"’

"ok":true,"session~i~d":"3546d87233636b04440e6e023c3e3018",
"source~l~ast~s~eq":6, "replication~i~d~v~ersion":2, "history":
["session~i~d":"3546d87233636b04440e6e023c3e3018", [...] "docs~r~ead":3,
"docs~w~ritten":3, "doc~w~rite~f~ailures":0 ]

Filter erstellen mit dynamischem Parameter (Auszug):

curl [...] -d ’"filters": "lang": "function(doc, req) return
req.query.lang == doc.lang " ’

Filter anwenden (Auszug):

curl [...] -d ’"source": "kina", "target": "anik", "create~t~arget":
true, "query~p~arams": "lang":"javascript" ’

Filter andwenden unter Angabe von genauen Dokument IDs (Auszug):

curl [...] -d ’"source": "kina", "target": "anik", "create~t~arget":
true, "doc~i~ds": ["id~1~", "id~2~", "id~n~"] ’

Datenbank replizieren mit der Datenbank \_replicate
---------------------------------------------------

Dokumentation unter \cite{11} zu finden

Änderungen in der DB verfolgen GET \_changes
--------------------------------------------

*(params)* since=sequence\_number, style=all\_docs, limit=n,
feed=continuous,longpolling, heartbeat=milliseconds filter=filter\_name,
include\_docs=true,false, timeout=milliseconds

curl -X GET "http://127.0.0.1:5984/kina/~c~hanges
?feed=continuous&heartbeat=2000"

"seq":3,"id":"kn0001","changes":
["rev":"3-5570e8bbb34412db757c2df4bfa1099b"]

"seq":4,"id":"fab911e6e9ed703bf536dccffe000937","changes":
["rev":"1-183f162a09532ccacf8223f6e2ba95f6"]

[...]

Compaction: Datenbank POST \_compact
------------------------------------

curl -X POST http://127.0.0.1:5984/kina/~c~ompact -H
"content-type:application/json"

"ok":true

Compaction: Design Dokumente POST \_compact/design-doc
------------------------------------------------------

curl -X POST http://127.0.0.1:5984/kina/~c~ompact/default -H
"content-type:application/json"

"ok":true

Compaction: Views POST \_view\_cleanup
--------------------------------------

curl -X POST http://127.0.0.1:5984/kina/~v~iew~c~leanup -H
"content-type:application/json"

"ok":true

Dokumente
=========

Prinzipiell sollte beim Erstellen auf IDs zurückgegriffen werden, die
CouchDB generiert. Allerdings macht es in manchen Fällen auch Sinn
eigene IDs zu nutzen.

UUIDs ausgeben GET \_uuids
--------------------------

*(params)* count=[n]

curl -X GET http://127.0.0.1:5984/~u~uids?count=3

"uuids": ["b7fc0ca7dffcc6d0f240c1e5bb000998",
"b7fc0ca7dffcc6d0f240c1e5bb000fe8", "b7fc0ca7dffcc6d0f240c1e5bb001c9a"]

Alle Dokumente erhalten GET \_all\_docs
---------------------------------------

*(params)* descending=true, key=key, st-artkey=key,
startkey\_docid=docid, endkey=key, endkey\_docid=docid,
group=true,false, group\_level=0-n, inclusive\_end=true,false, limit=n,
reduce=true,false, skip=n, stale=ok, update\_seq=true,false

curl -X GET http://127.0.0.1:5984/kina/~a~ll~d~ocs

"total~r~ows":12,"offset":0,"rows":[
"id":"7341477ce373f9cc76f351e5980008bb",
"key":"7341477ce373f9cc76f351e5980008bb",
"value":"rev":"2-f0bfca3976ad04bce05b2ade242519d7",
"id":"7341477ce373f9cc76f351e5980015cd",
"key":"7341477ce373f9cc76f351e5980015cd",
"value":"rev":"2-d6f5f2cb326c1f68f95d2bfbef329280"]

Mehrere Dokumente mit key(id) erhalten POST \_all\_docs
-------------------------------------------------------

*(params)* siehe: Alle Dokumente erhalten *(!)* gut nutzbar, um mit
einem Request mehrere Dokumente zu erhalten

curl -X POST http://127.0.0.1:5984/kina/~a~ll~d~ocs?include~d~ocs=true
-H "content-type:application/json" -d ’"keys": [
"fab911e6e9ed703bf536dccffe000937", "7341477ce373f9cc76f351e5980015cd"
]’

"total~r~ows":12,"offset":0,"rows":[
"id":"fab911e6e9ed703bf536dccffe000937",
"key":"fab911e6e9ed703bf536dccffe000937",
"value":"rev":"1-183f162a09532ccacf8223f6e2ba95f6",
"doc":"~i~d":"fab911e6e9ed703bf536dccffe000937",
"~r~ev":"1-183f162a09532ccacf8223f6e2ba95f6", "lang":"javascript",
"id":"7341477ce373f9cc76f351e5980015cd",
"key":"7341477ce373f9cc76f351e5980015cd",
"value":"rev":"3-99b698d0caba3c9892a385dd0efe2a3d",
"doc":"~i~d":"7341477ce373f9cc76f351e5980015cd",
"~r~ev":"3-99b698d0caba3c9892a385dd0efe2a3d", "lang":"lisp" ]

Dokument erstellen mit CouchDB ID PUT
-------------------------------------

curl -X PUT http://127.0.0.1:5984/kina/ b7fc0ca7dffcc6d0f240c1e5bb000998

"ok":true,"id":"b7fc0ca7dffcc6d0f240c1e5bb000998",
"rev":"1-967a00dff5e02add41819138abb3284d"

Dokument erstellen mit eigener ID PUT
-------------------------------------

curl -X PUT http://127.0.0.1:5984/kina/kn0001 -d ’’

"ok":true,"id":"kn0001", "rev":"3-5570e8bbb34412db757c2df4bfa1099b"

Dokument erstellen POST
-----------------------

curl -X POST http://127.0.0.1:5984/kina/ -H "content-type:
application/json" -d ’’

"ok":true,"id":"b7fc0ca7dffcc6d0f240c1e5bb000fe8",
"rev":"1-367b00dfc5e02axd41819138abb3284d"

Dokument anfragen GET
---------------------

*(params)* rev=[revision], revs=true, revs\_info=true

curl -X GET http://127.0.0.1:5984/kina/ b7fc0ca7dffcc6d0f240c1e5bb000998

"~i~d":"b7fc0ca7dffcc6d0f240c1e5bb000998",
"~r~ev":"1-367b00dfc5e02axd41819138abb3284d", "inhalt":"hier steht was"

Dokument erweitern / aktualisieren PUT
--------------------------------------

*(params)* rev=revision *(header)* if-match=revision

curl -X PUT http://127.0.0.1:5984/kina/ fab911e6e9ed703bf536dccffe000937
-d ’"~r~ev":"1-183f162a09532ccacf8223f6e2ba95f6", "lang": "php"’

curl -X PUT http://127.0.0.1:5984/kina/ fab911e6e9ed703bf536dccffe000937
-H "if-match:1-183f162a09532ccacf8223f6e2ba95f6" -d ’"lang":"php"’

"ok":true,"id":"fab911e6e9ed703bf536dccffe000937",
"rev":"2-cf8bad0865d8296870352617ab3afdba"

Dokument löschen DELETE
-----------------------

*(params)* rev=revision *(header)* if-match=revision *(!)* Dokument wird
nur aus dem Index gelöscht und markiert mit deleted:true

curl -X DELETE http://127.0.0.1:5984/kina/
7341477ce373f9cc76f351e598001cdd ?rev=1-9533bf3b3c5cda717ed000b186fdafae

curl -X DELETE http://127.0.0.1:5984/kina/
7341477ce373f9cc76f351e598001cdd -H
"if-match:1-9533bf3b3c5cda717ed000b186fdafae"

"ok":true,"id":"7341477ce373f9cc76f351e598001cdd",
"rev":"2-5c7fb5dfeaf6f7cea149922fa1cdaf96"

Dokument-Attachment speichern PUT
---------------------------------

*(params)* rev=revision *(headers)* content-length:bytes,
content-type:MIME-type document, if-match:revision

*(!)* generell könnte auch nur die Option data für eine reine Textdatei
verwendet werden

curl -X PUT http://127.0.0.1:5984/kina/
b7fc0ca7dffcc6d0f240c1e5bb000998/
geburtstag.txt?rev=2-1726cd3e40ffd356591114f014b1ac22 –data-binary
@geburtstag.txt -H "content-type: text/plain;charset=utf-8"

HTTP/1.1 201 Created Server: CouchDB/1.1.0 (Erlang OTP/R14B03) Location:
http://127.0.0.1:5984/datenbankname/
b7fc0ca7dffcc6d0f240c1e5bb000998/geburtstag.txt Etag:
"2-1726cd3e40ffd356591114f014b1ac22" Date: Mon, 07 Feb 2011 22:53:25 GMT
Content-Type: text/plain;charset=utf-8 Content-Length: 66 Cache-Control:
must-revalidate

"ok":true,"id":"b7fc0ca7dffcc6d0f240c1e5bb000998",
"rev":"2-1726cd3e40ffd356591114f014b1ac22"

Dokument-Attachment anfragen GET
--------------------------------

curl -X GET http://127.0.0.1:5984/kina/
b7fc0ca7dffcc6d0f240c1e5bb000998/geburtstag.txt

HTTP/1.1 200 OK Server: CouchDB/1.1.0 (Erlang OTP/R14B03) ETag:
"2-1726cd3e40ffd356591114f014b1ac22" Date: Sun, 28 Aug 2011 20:32:14 GMT
Content-Type: text/plain;charset=utf-8 Content-Length: 66 Cache-Control:
must-revalidate Accept-Ranges: none

Dokument-Attachment löschen DELETE
----------------------------------

*(params)* rev=revision *(headers)* if-match:revision

curl -X GET http://127.0.0.1:5984/kina/
b7fc0ca7dffcc6d0f240c1e5bb000998/geburtstag.txt

"ok":true,"id":"b7fc0ca7dffcc6d0f240c1e5bb000998",
"rev":"3-f1d8176c2ea671ad75bd85a55dea4d05"

Dokument permanent löschen POST \_purge
---------------------------------------

*(!)* Refernez zum Dokument wird gelöscht; wird nicht repliziert. Um
denPlattenplatz auch frei zu bekommen, muss \_compact ausgeführt werden

curl -X POST http://127.0.0.1:5984/kina/~p~urge/ -H
"content-type:application/json" -d ’"7341477ce373f9cc76f351e598001cdd":
["2-5c7fb5dfeaf6f7cea149922fa1cdaf96"] ’

"purge~s~eq":1,"purged": "7341477ce373f9cc76f351e598001cdd":
["2-5c7fb5dfeaf6f7cea149922fa1cdaf96"]

Dokument Info HEAD
------------------

*(params)* rev=revision, revs=true,false, revs\_info=true,false

curl –head -X GET http://127.0.0.1:5984/kina/
fab911e6e9ed703bf536dccffe000937?revs=true

HTTP/1.1 200 OK Server: CouchDB/1.1.0 (Erlang OTP/R14B03) Etag:
"1-183f162a09532ccacf8223f6e2ba95f6" Date: Sun, 28 Aug 2011 14:40:35 GMT
Content-Type: text/plain;charset=utf-8 Content-Length: 175
Cache-Control: must-revalidate

Dokument kopieren COPY
----------------------

*(i)* Die ID 7341477ce373f9cc76f351e598001d4c wurde zuerst per \_uuids
request erfragt. Um in ein bestehendes Dokument zu kopieren, wird die ID
des Zieldokuments angegeben *(header)* Destination:docid *(params)*
rev=revision

curl -X COPY http://127.0.0.1:5984/kina/
fab911e6e9ed703bf536dccffe000937 -H
"destination:7341477ce373f9cc76f351e598001d4c"

"id":"7341477ce373f9cc76f351e598001d4c",
"rev":"1-5a5db37cb3735e769ea8d133b28f04a1"

Mehrere Dokumente anlegen POST \_bulk\_docs
-------------------------------------------

*(options)* all\_or\_nothing:true,false (=non-atomic=default),
docs:key:value,...

curl -X POST http://127.0.0.1:5984/kina/~b~ulk~d~ocs -H
"content-type:application/json" -d ’"docs":[ "lang":"c", "lang":"c++" ]’

["id":"7341477ce373f9cc76f351e5980008bb",
"rev":"1-f491c132ea24ecf492e0b5ae18467876",
"id":"7341477ce373f9cc76f351e5980015cd",
"rev":"1-9533bf3b3c5cda717ed000b186fdafae"]

Mehrere Dokumente aktualisieren POST \_bulk\_docs
-------------------------------------------------

*(options)* all\_or\_nothing:true,false (=non-atomic=default),
docs:key:value,...

curl -X POST http://127.0.0.1:5984/kina/~b~ulk~d~ocs -H
"content-type:application/json" -d ’"docs":[
"~i~d":"7341477ce373f9cc76f351e5980008bb", "~r~ev":
"1-f491c132ea24ecf492e0b5ae18467876", "lang":"erlang",
"~i~d":"7341477ce373f9cc76f351e5980015cd",
"~r~ev":"1-9533bf3b3c5cda717ed000b186fdafae", "lang":"lisp" ]’

["id":"7341477ce373f9cc76f351e5980008bb",
"rev":"2-f0bfca3976ad04bce05b2ade242519d7",
"id":"7341477ce373f9cc76f351e5980015cd",
"rev":"2-d6f5f2cb326c1f68f95d2bfbef329280"]

sotief

1<http://couchdb.apache.org>
2<https://cloudant.com/futon/database.html?andywenk%2Fkina>
3<http://wiki.apache.org/couchdb/Reference> 4<http://www.erlang.org>
5<http://en.wikipedia.org/wiki/Representational_State_Transfer>
6<http://en.wikipedia.org/wiki/ACID>
7<http://en.wikipedia.org/wiki/Multiversion_concurrency_control>
8<http://www.julianbrowne.com/article/viewer/brewers-cap-theorem>
9<http://en.wikipedia.org/wiki/MapReduce> 10<http://curl.haxx.se/>
11<https://gist.github.com/832610>
