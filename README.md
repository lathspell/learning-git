GIT
===

Hilfe
-----

    git --help                  Liste der wichtigsten Befehle
    git --help -a               Liste aller Befehler (scary)
    git --help -m               Liste alle Konzepe zu denen es eingebaute Hilfe gibt

    git help glossary           Erklärung der Begriffe rund um GIT
    git help revisions          Erklärung der Revionskürzel wie "HEAD^"
    git help tutorial           Ein kleines Tutorial

    . /usr/share/bash-completion/completions/git    Aktiviert Bash-Completion für Git
    
    git instaweb --httpd /usr/sbin/apache2          Startet integrierte Web-GUI zur Ansicht
Basics
------

Repository Erstellen:
    git init                    Erzeugt ein Repo, d.h. ein .git Verzeichnis
    git clone                   Erzeugt ein Repository auf Basis eines bestehenden, local oder remote repository.

Änderungen ansehen
    git diff                    Unterschiede zwischen Index und Arbeitsverzeichnis (modifizierte Dateien, die noch nicht mit "git add" hinzugefügt wurden)
    git diff <COMMIT>           Unterschiede zwischen einem Commit (auch HEAD) und dem Arbeitsverzeichnis
    git diff --cached           Unterschiede zwischen einem Commit (auch HEAD) und dem Index (mit "git add" markierten Dateien)
    git diff <COMMIT> <COMMIT>  Unterschiede zwischen zwei Commits (auch HEAD und ein Tag)

    git log                     Zeigt die Historie des aktuellen Branch
    git log --follow <FILE>     Zeigt die Historie einer Datei inklusive Umbenennungen
    git log -5 --decorate=full  Zeigt die letzten 5 Einträge der Historie mit Tags
    
    git show                    Zeigt aktuelle Änderungen
    git show <COMMIT>           Zeigt Änderungen eines Commits
    git show-branch --more=10   Zeigt die letzten 10 Commit-Messages

Transaktionen
    git add <FILE>              Aktueller Stand -> Objekt und Eintrag in Index ("stage to commit")
                                (Überflüssige, weil unreferenzierte, Objekte werden durch automatisches "git gc" wieder gelöscht)

    git reset <FILE>            Löscht Objekt und Index-Eintrag für Objekt ("unstage for commit"), behält aber lokale Datei.
    git reset --hard <FILE>     Löscht sowohl Objekt/Index als auch die lokalen Änderungen

    git rm <FILE>               Löscht lokale Datei und markiert sie zur Löschung im Index
    git rm --cached <FILE>      Markiert Datei zur Löschung im Index, behält sie aber lokal.
    git mv <FILE> <FILE>        Benennt eine lokale Datei um und passt den Index entsprechend an.

    git commit                  Alle Änderungen laut Index in einem Commit-Objekt zusammenfassen
                                (Technisch: "git write-tree" gefolgt von "git write-commit")
    git commit -a               Commit mit vorherigendem "git add"

Branches
    git branch -a               Listet alle Branches (mit "-a" auch Remotes)
    git branch <BRANCH>         Erstellt einen neuen Branch
    git checkout <BRANCH>       In einen Branch wechseln
    git checkout -b <BRANCH>    Branch erstellen und direkt dorthin wechseln (wie "git branch" und "git checkout")
    git merge <BRANCH>          Erstellt einen Merge-Commit (z.B. in master mit "bug10" als angegebenen Branch)
    
Remote Repositories
    git fetch                   Holt alle oder bestimmte Objekte und damit auch deren Historie aber ändert noch nicht an den lokalen Dateien.
    git pull                    Holt neue Änderungen aus einem anderen Repository
    git push                    Überträgt Änderungen in ein anderes Repository

Fehler suchen
-------------

    git bisect start            Erzeugt einen Internen-Branch auf Basis von HEAD mit einigen Meta-Daten für die Binary-Section.
    git bisect good [<COMMIT>]  Sagt bisect das eine bzw. die aktuelle Version funktioniert; welchselt den Standpunkt
    git bisect bad [<COMMIT>]   Sagt bisect, daß eine bzw. die aktuelle Version nicht funktioniert; wechselt den Standpunkt
    git bisect log              Zeigt alle bisher als gut oder schlecht markierten Versionen
    git bisect reset            Beendet das Bisect

    git blame <FILE>            letzte Anderungen zeigen

    git log -S<TEXT> -p         Zeigt alle commits bei denen TEXT im Diff auftaucht ("Pickaxe" Suche)
   
(internals) Datenbank angucken
------------------------------

    git ls-files --stage                    Zeigt Hash aller Dateien an

    git rev-parse <HASH|Tag>                Zeigt den kompletten Hash zu einem Hash oder einem Tag.

    zlib-flate -uncompress < <OBJ-FILE>     Datei entpacken und angucken, könnte Binär sein!
    git cat-file -t <HASH>                  Typ dieses BLOBs anzeigen
    git cat-file -p <HASH>                  Inhalt dieses BLOBs anzeigen

    git hash-object <FILE>                  Berechnet nur den aktuellen Hash
    git hash-object --write <FILE>          Erstellt BLOB Objekt und liefert Hash (Teil von "add").

    git write-tree                          Erstellt Tree Objekt der aktuellen Dateien und liefert Hash (Teil von "commit").
    git commit-tree <HASH>                  Erstellt Commit Objekt für einen Tree (Teil von "commit").


Konfiguration
-------------

    Rangfolge Konfigurationsdateien 
        --param                 CLI 
        .git/config             "local"
        ~/.gitconfig            "global"
        /etc/gitconfig          "system"

    git config [|--local|--global|--system] --list
    git config user.name "Max Mustermann"
    git config user.email "mmustermann@example.com"
    
    .git/description            ???

    .gitignore                  Verzeichnis-lokale Ignorier-Liste, ist selbst aber im SCM
    git ls-files --other --ignored --exclude-standard       Zeigt alle irgendwie ignorierten Dateien.
    
    export GIT_EDITOR=vim       Falls der System-Editor ein anderer ist


    git config --global alias.my-log \          Definiert einen Alias der mit "git my-show-graph" aufgerufen werden kann.
        "log --graph --all --full-history --pretty=format:'%Cred%h%Creset %x09 %ad %C(bold blue)<%ae>%Creset %C(yellow)%d%Creset %s'"

Konzepte
--------

    Datentypen:
    * BLOB
    * Tree
    * Commit
    * Tag

    Datenbereiche:
    * Index/Cache
        Index kann jederzeit gelöscht und aus den Objekten neu aufgebaut werden.
    * Objekte

        $ zlib-flate -uncompress < .git/objects/a0/1ee289f9a3c65287845c5138783d4f3b24a443
        blob 7.*.swp

        $ zlib-flate -uncompress < .git/objects/b0/7237be0ee6ede33983935620532aeaf581e14a | strings
        tree 75
        100644 .gitignore
        \Q8x=O;$C                    // Hash von .gitignore (0a1e..43), mit "od -xa" verifizierbar
        100644 README.md
        .........                    // Hash von README.md

        $ zlib-flate -uncompress < .git/objects/c8/c3c96e9f776d2d2e5e8fc097104818a68d8355
        commit 222
        tree b07237be0ee6ede33983935620532aeaf581e14a
        parent fb21eb2b5c0dba041380ab8e48f837c1ba26dbc8
        author Christian Brunotte <cb@lathspell.de> 1436045859 +0200
        committer Christian Brunotte <cb@lathspell.de> 1436045859 +0200

        3


Unterschiede zu SVN
-------------------
    
    Verzeichnisstruktur vs. Object+Index
        SVN merkt sich die Verzeichnisstruktur, commit scannt tatsächliche Dateien. 

    add/commit
        SVN: Wenn Datei verschoben wird, ist es quasi eine alte die wegfällt und eine neue, die dazu kommt.
        GIT: merkt sich nur einen Haufen von Dateien mit Attributen zu denen auch der Pfad gehört, wird eine Datei verschoben, ändert sich nur eines ihrer Attribute. Doppelte Dateien werden
        nur einmal gespeichert, aber mit zwei Verweisen im Index.


GUI
---
    gitg            Sehr schön um Branches zu visualisieren
    gitk
    git-cola

Links
-----
    
    GIT Cheat Sheet
    https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf

    Interaktive Cheat Sheet
    http://ndpsoftware.com/git-cheatsheet.html

