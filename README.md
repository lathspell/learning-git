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
    git diff <COMMIT> <COMMIT>  Unterschiede zwischen zwei Commits (auch HEAD und ein Tag); Vergleicht "Snapshots", also die Tree-Objekte (vgl. "git log"!)

    git log                     Zeigt die Historie des aktuellen Branch
    git log --follow <FILE>     Zeigt die Historie einer Datei inklusive Umbenennungen
    git log -5 --decorate=full  Zeigt die letzten 5 Einträge der Historie mit Tags
    git log -p <COMMIT>..<COMMIT>       Zeigt Unterschiede zwischen gemeinsamen Vorfahren und von A und B und dem Commit B (vgl. "git diff"!) <-- ACHTUNG!
    
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

Branches und Merges
    git branch -a               Listet alle Branches (mit "-a" auch Remotes)
    git branch <BRANCH>         Erstellt einen neuen Branch
    git checkout <BRANCH>       In einen Branch wechseln
    git checkout -b <BRANCH>    Branch erstellen und direkt dorthin wechseln (wie "git branch" und "git checkout")
    git merge <BRANCH>          Erstellt einen Merge-Commit (z.B. in master mit "bug10" als angegebenen Branch)
    git merge --ff-only/--no-ff Bei einem Merge von einem Upstream Repository, wie es z.B. durch "git pull" gemacht wird,
                                werden, sofern es keine lokalen Änderungen gibt, einfach die Upstream Commits importiert.
                                In der Historie sieht es also nicht wie ein "Branch+Merge" sondern wie eine gerade Linie
                                aus. Das nennt man "fast-forward".

    git ls-files -u                     Zeigt die "unmerged" Dateien in allen Versionen (für "git show <HASH>")

                                        ```
                                        $ git ls-files -u
                                        100644 8129f257ec5b9d1218cd3118c7806bebe161be57 1       datei1          <- Basis
                                        100644 dc14b598cbc78d0bddcfd1b4e5bb4e5804883363 2       datei1          <- unser Branch
                                        100644 13cb3f61f1cb7bb0d666bc9916c0544a348e3bba 3       datei1          <- der zu mergende Branch
                                        ```

    git diff MERGE_HEAD                 Variante von "git diff HEAD" aber für den zu mergenden Branch
    git diff :1:datei1 :2:datei1        Vergleicht die Versionen 1 und 2 (nach "git ls-files -u" Notation) der Datei "datei1"

                                        ```
                                        $ git diff :2:datei1 :3:datei1
                                        diff --git a/:2:datei1 b/:3:datei1
                                        index dc14b59..13cb3f6 100644
                                        --- a/:2:datei1
                                        +++ b/:3:datei1
                                        @@ -1,2 +1,2 @@
                                         pr3
                                        -Fr 10. Jul 18:33:41 CEST 2015
                                        +Fr 10. Jul 18:34:16 CEST 2015
                                        ```

    git log --merge --left-right -p     Zeigt bei Merge-Konflikten die problematischen Diffs
                                        ("-p" kann auch hier nach Stichwort suchen!)

                                        ```
                                            $ git log --merge --left-right -p
                                        commit > 80d068dd6bf2d86f0afb70bba459b8b8253d4c78
                                        Author: Christian Brunotte <cb@lathspell.de>
                                        Date:   Fri Jul 10 18:34:22 2015 +0200

                                            pr-3 branch

                                        diff --git a/datei1 b/datei1
                                        index 8129f25..13cb3f6 100644
                                        --- a/datei1
                                        +++ b/datei1
                                        @@ -1 +1,2 @@
                                        -Fr 10. Jul 18:32:37 CEST 2015
                                        +pr3
                                        +Fr 10. Jul 18:34:16 CEST 2015

                                        commit < 92e7c6978fcef03f4f56d77284cacf149c1e72c5
                                        Author: Christian Brunotte <cb@lathspell.de>
                                        Date:   Fri Jul 10 18:34:06 2015 +0200

                                            pr-3 master

                                        diff --git a/datei1 b/datei1
                                        index 8129f25..dc14b59 100644
                                        --- a/datei1
                                        +++ b/datei1
                                        @@ -1 +1,2 @@
                                        -Fr 10. Jul 18:32:37 CEST 2015
                                        +pr3
                                        +Fr 10. Jul 18:33:41 CEST 2015
                                        ```
    git checkout MERGE_HEAD -- datei1   Gezielt "datei1" mit der Version des zu mergenden Branch überschreiben (z.B. weil sie Conflicts hatte)

    git commit                          Hier ohne Optionen committet den Merge mit vorgefertigter Meldung aus .git/MERGE_MSG
    
    git merge --abort                   Resettet die Merge Operation *vor* dem Commit
    git reset --hard HEAD               Resettet die Merge Operation *vor* dem Commit
    git reset --hard ORIG_HEAD          Resettet den letzten Merge *nach* dem Commit
    
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

    ```
    * f355408        Wed Jul 8 20:43:56 2015 +0200 <cb@lathspell.de>  typo
    *   1c7ba74      Wed Jul 8 20:42:42 2015 +0200 <cb@lathspell.de>  Merge branch 'bug10'
    |\  
    | * 3cc59c3      Wed Jul 8 20:37:53 2015 +0200 <cb@lathspell.de>  (bug10) added client gitg
    | * b192e13      Wed Jul 8 19:02:10 2015 +0200 <cb@lathspell.de>  working in branach bug10
    * | fd4568d      Wed Jul 8 19:09:27 2015 +0200 <cb@lathspell.de>  cleanup in master
    |/  
    * 75d7f86        Tue Jul 7 22:08:41 2015 +0200 <cb@lathspell.de>  m
    ```

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

Central vs. Distributed
    SVN arbeitet immer mit einem zentralen Repository.
    Git hat in jedem Arbeitsverzeichnis ein vollwertiges Repository und kann das mit beliebig vielen Remote Repositories abgleichen.
    => Git kommt temporär ohne Netzwerkverbindung aus.
    => Git kann aus mehrere Quellen verfolgen um daraus selektiv einzelne Commits zu mergen.

Diffs vs. Snapshots
    SVN arbeitet auf Basis von diffs und speichert Commits auch als solche in den Metadaten.
    Git speichert deduplizierte Snapshots der kompletten Dateien und Verzeichnisbäume.
    => Bei vielen kleinen Änderungen an großen Dateien braucht SVN weniger Platz mit seinen Diffs als Git mit seinen vielen Snapshots.
    => Beim Vergleich von Älteren Commits muss SVN den jeweiligen Stand immer aus den Diffs aufbauen während Git die Objekte sofort verfügbar hat und daher schneller ist.
    => Das Umbenennen/Verschieben einer Datei ist bei SVN eigentlich ein add/remove während Git nur eine Zeile im Tree-Objekt ändern muss.
    => Git speichert inhaltlich identische Dateien effektiver (was aber kaum vorkommt, Branches/Tags sind auch bei SVN "cheap"!)

Staging Area
    SVN kennt nur das Arbeitsverzeichnis und das Repository.
    Git hat zwischen Arbeitsverzeichnis und Repository noch die Staging Area im Index in der Transaktionen vorbereitet werden.
    => Git ist verwirrender :-/



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

