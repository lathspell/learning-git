GIT
===

Basics
------

    git init                    Erzeugt ein Repo
                                Erstellt .git Verzeichnis mit Datenbank

    git add <FILES>             Aktueller Stand -> Index

    git commit                  Alle Änderungen im Index zusammenfassen

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

Unterschiede zu SVN
-------------------
    
    Verzeichnisstruktur vs. Object+Index
        SVN merkt sich die Verzeichnisstruktur, commit scannt tatsächliche Dateien. 

    add/commit
        SVN: Wenn Datei verschoben wird, ist es quasi eine alte die wegfällt und eine neue, die dazu kommt.
        GIT: merkt sich nur einen Haufen von Dateien mit Attributen zu denen auch der Pfad gehört, wird eine Datei verschoben, ändert sich nur eines ihrer Attribute. Doppelte Dateien werden
        nur einmal gespeichert, aber mit zwei Verweisen im Index.

