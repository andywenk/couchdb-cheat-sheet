# CouchDB-Cheat-Sheet

Autor: Andreas Wenk  
Version: 0.0.1

Dieses Cheat-Sheet ist eine Übersicht der RESTful CouchDB-API. 

## Überblick

## Datenbanken

### Datenbankinformationen erhalten [**GET**]

>     curl -X GET http://127.0.0.1:5984/datenbankname
>     {"db_name":"datenbankname",
>      "doc_count":21,
>      "doc_del_count":56,
>      "update_seq":454,
>      "purge_seq":0,
>      "compact_running":false,
>      "disk_size":454750,
>      "instance_start_time":"1311337314684694",
>      "disk_format_version":5,
>      "committed_update_seq":454}

### Alle Datenbanken im Cluster [**GET**]

>     curl -X GET http://127.0.0.1:5984/_all_dbs
>     ["_replicator","_users"]

### Datenbank erstellen [**PUT**]

>     curl -X PUT http://127.0.0.1:5984/datenbankname
>     {"ok": true}

### Datenbank löschen [**DELETE**]

>     curl -X DELETE http://127.0.0.1:5984/datenbankname
>     {"ok": true}

### Datenbank replizieren [**POST**]

>     curl -X POST http://127.0.0.1:5984/_replicate 
>          -H "content-type:application/json" 
>          -d '{"source": "datenbankname", "target": "datenbankname_neu"}'
>     {"ok":true, "session_id":"f75eb944bac70f40e77953f484afb64c", 
>      "source_last_seq":36, "history":
>        [{"session_id":"f75eb944bac70f40e77953f484afb64c",
>          "start_time":"Thu, 14 Apr 2011 20:36:12 GMT", 
>          "end_time":"Thu, 14 Apr 2011 20:36:12 GMT",
>          "start_last_seq":0,
>          "end_last_seq":36,
>          "recorded_seq":36,
>          "missing_checked":0,
>          "missing_found":14,
>          "docs_read":14,
>          "docs_written":14,
>          "doc_write_failures":0
>       }]
>     }

## Dokumente

### UUIDs ausgeben [**GET**]

*(params)* count=[n]

>     curl -X GET http://127.0.0.1:5984/_uuids?count=3
>     {"uuids":
>       ["b7fc0ca7dffcc6d0f240c1e5bb000998",
>        "b7fc0ca7dffcc6d0f240c1e5bb000fe8",
>        "b7fc0ca7dffcc6d0f240c1e5bb001c9a"]
>     }

### Alle Dokumente erhalten [**GET**]

*(params)* descending=true

### Dokument erstellen [**PUT**]

>     curl -X PUT http://127.0.0.1:5984/datenbankname/b7fc0ca7dffcc6d0f240c1e5bb000998
>     {"ok":true,"id":"b7fc0ca7dffcc6d0f240c1e5bb000998",
>      "rev":"1-967a00dff5e02add41819138abb3284d"}

### Dokument erstellen [**POST**]

>     curl -X POST http://127.0.0.1:5984/datenbankname/
>          -H "content-type: application/json"
>          -d '{}'
>     {"ok":true,"id":"b7fc0ca7dffcc6d0f240c1e5bb000fe8",
>      "rev":"1-367b00dfc5e02axd41819138abb3284d"}

### Dokument anfragen [**GET**]

*(params)* rev=[revision], revs=true, revs_info=true
 
>     curl -X GET http://127.0.0.1:5984/datenbankname/b7fc0ca7dffcc6d0f240c1e5bb000998
>     {"_id":"b7fc0ca7dffcc6d0f240c1e5bb000998",
>      "_rev":"1-367b00dfc5e02axd41819138abb3284d",
>      "inhalt":"hier steht was"}

### Dokument erweitern / aktualisieren [**PUT**]

>     curl -X PUT http://127.0.0.1:5984/datenbankname/b7fc0ca7dffcc6d0f240c1e5bb000998
>          -d '{"_rev":"1-367b00dfc5e02axd41819138abb3284d",
>               "neuer Inhalt": "kommt hier hin",
>               "inhalt": "bestehender wird aktualisiert"}'
>     {"ok":true,"id":"7fc0ca7dffcc6d0f240c1e5bb000998",
>      "rev":"2-53f21467344e4cb88384fc9e2e189049"}           

### Dokument-Attachment speichern [**PUT**]

*(!)* generell könnte auch nur die Option --data für eine reine Textdatei verwendet werden

>     curl -X PUT http://127.0.0.1:5984/datenbankname/b7fc0ca7dffcc6d0f240c1e5bb000998/ \
>                  geburtstag.txt?rev=2-53f21467344e4cb88384fc9e2e189049
>          --data-binary @geburtstag.txt
>          -H "content-type: text/plain;charset=utf-8"
>     HTTP/1.1 201 Created
>     Server: CouchDB/1.1.0 (Erlang OTP/R14B03)
>     Location: http://127.0.0.1:5984/datenbankname/b7fc0ca7dffcc6d0f240c1e5bb000998/ \
>               geburtstag.txt
>     Etag: "3-d5171a67e9e27aeba9beac32149a86a9"
>     Date: Mon, 07 Feb 2011 22:53:25 GMT
>     Content-Type: text/plain;charset=utf-8
>     Content-Length: 95
>     Cache-Control: must-revalidate
>
>     {"ok":true,"id":"b7fc0ca7dffcc6d0f240c1e5bb000998",
>      "rev":"3-d5171a67e9e27aeba9beac32149a86a9"}

### Dokument löschen [**DELETE**]

>     curl -X DELETE http://127.0.0.1:5984/datenbankname/b7fc0ca7dffcc6d0f240c1e5bb000998
>                     ?rev=3-d5171a67e9e27aeba9beac32149a86a9
>     {"ok":true,"id":"b7fc0ca7dffcc6d0f240c1e5bb000998",
>      "rev":"3-d5171a67e9e27aeba9beac32149a86a9"}

### Dokument Info [**HEAD**]

### Dokument kopieren [**COPY**]

### Mehrere Dokumente gleichzeitig anlegen [**POST**]
