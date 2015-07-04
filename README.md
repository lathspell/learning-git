GIT
===

Basics
------

    git init                    Erzeugt ein Repo
                                Erstellt .git Verzeichnis mit Datenbank

    git add <FILES>             Aktueller Stand -> Objekt und Eintrag in Index

    git commit                  Alle Änderungen laut Index in einem Commit-Objekt zusammenfassen

Konfiguration
-------------
    
    CLI                         Rangfolge Konfigurationsdateien 
    .git/config
    ~/.gitconfig
    /etc/gitconfig              
    
    .git/description            ???

    .gitignore                  Verzeichnis-lokale Ignorier-Liste, ist selbst aber im SCM                         
    
    export GIT_EDITOR=vim


Konzepte
--------

    Datentypen:
    * BLOB
    * Tree
    * Commit
    * Tag

    Datenbereiche:
    * Index/Cache
    * Objekte

        $ file ./.git/objects/a0/1ee289f9a3c65287845c5138783d4f3b24a443
        ./.git/objects/a0/1ee289f9a3c65287845c5138783d4f3b24a443: zlib compressed data
        
        $ cat ./.git/objects/a0/1ee289f9a3c65287845c5138783d4f3b24a443 | zlib-flate -uncompress  
        blob 7.*.swp

        Index kann jederzeit gelöscht und aus den Objekten neu aufgebaut werden.


xxxxxxxxxxxxxxxxxxxxx

Unterschiede zu SVN
-------------------
    
    Verzeichnisstruktur vs. Object+Index
        SVN merkt sich die Verzeichnisstruktur, commit scannt tatsächliche Dateien. 

    add/commit
        SVN: Wenn Datei verschoben wird, ist es quasi eine alte die wegfällt und eine neue, die dazu kommt.
        GIT: merkt sich nur einen Haufen von Dateien mit Attributen zu denen auch der Pfad gehört, wird eine Datei verschoben, ändert sich nur eines ihrer Attribute. Doppelte Dateien werden
        nur einmal gespeichert, aber mit zwei Verweisen im Index.

