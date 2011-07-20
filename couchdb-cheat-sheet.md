# CouchDB-Cheat-Sheet

Autor: Andreas Wenk

Version: 0.0.1

Dieses Cheat-Sheet ist eine Übersicht der RESTful CouchDB API. 

## Überblick

## Datenbanken

### PUT - Datenbank erstellen

**HTTP**

>     PUT http://127.0.0.1:5984/datenbankname  
>     content-type: application/json

**curl**

>     curl -X PUT http://127.0.0.1:5984/datenbankname

**response**

>     {"ok": true}


