CLI
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

Repository Erstellen
--------------------

    git init                    Erzeugt ein Repo, d.h. ein .git Verzeichnis
    git init --bare             Erzeugt ein "bare" Repo, d.h. eines ohne Arbeitsverzeichnis, auf einem zentralen Git-Server
    git clone                   Erzeugt ein Repository auf Basis eines bestehenden, local oder remote repository.

Änderungen ansehen
------------------

    git diff                    Unterschiede zwischen Index und Arbeitsverzeichnis (modifizierte Dateien, die noch nicht mit "git add" hinzugefügt wurden)
    git diff <COMMIT>           Unterschiede zwischen einem Commit (auch HEAD) und dem Arbeitsverzeichnis
    git diff --cached           Unterschiede zwischen einem Commit (auch HEAD) und dem Index (mit "git add" markierten Dateien)
    git diff <COMMIT> <COMMIT>  Unterschiede zwischen zwei Commits (auch HEAD und ein Tag); Vergleicht "Snapshots", also die Tree-Objekte (vgl. "git log"!)

    git format-patch <COMMIT>   Erzeugt eine hübsche Diff Datei

    git log                     Zeigt die Historie des aktuellen Branch
    git log --follow <FILE>     Zeigt die Historie einer Datei inklusive Umbenennungen
    git log -5 --decorate=full  Zeigt die letzten 5 Einträge der Historie mit Tags
    git log -p <COMMIT>..<COMMIT>       Zeigt Unterschiede zwischen gemeinsamen Vorfahren und von A und B und dem Commit B (vgl. "git diff"!) <-- ACHTUNG!
    
    git show                    Zeigt aktuelle Änderungen
    git show <COMMIT>           Zeigt Änderungen eines Commits
    git show-branch --more=10   Zeigt die letzten 10 Commit-Messages

Transaktionen
-------------

    git add <FILE>              Aktueller Stand -> Objekt und Eintrag in Index ("stage to commit")
                                (Überflüssige, weil unreferenzierte, Objekte werden durch automatisches "git gc" wieder gelöscht)

    git reset [--mixed] <FILE>  Löscht Objekt und Index-Eintrag für Objekt ("unstage for commit"), behält aber lokale Datei.
    git reset --hard <FILE>     Löscht sowohl Objekt/Index als auch die lokalen Änderungen

    git rm <FILE>               Löscht lokale Datei und markiert sie zur Löschung im Index
    git rm --cached <FILE>      Markiert Datei zur Löschung im Index, behält sie aber lokal.
    git mv <FILE> <FILE>        Benennt eine lokale Datei um und passt den Index entsprechend an.

    git commit                  Alle Änderungen laut Index in einem Commit-Objekt zusammenfassen
                                (Technisch: "git write-tree" gefolgt von "git write-commit")
    git commit -a               Commit mit vorherigendem "git add"

Branches und Merges
-------------------

    git branch -a -vv           Listet alle Branches (mit "-a" auch Remotes, mit "-vv" auch "--track" Markierung)
    git branch <BRANCH>         Erstellt einen neuen Branch
    git checkout <BRANCH>       In einen Branch wechseln
    git checkout -b <BRANCH>    Branch erstellen und direkt dorthin wechseln (wie "git branch" und "git checkout")
    git merge <BRANCH>          Erstellt einen Merge-Commit (z.B. in master mit "bug10" als angegebenen Branch)
    git merge --ff-only/--no-ff Bei einem Merge können, wenn der lokale Branch zwischenzeitlich keine Commits hatte,
                                die Commits des anderen Branch entweder einfach als nachfolgende Commits importiert
                                werden, so das es im Log wie eine gerade Linie aussieht ("fast-forward") oder über einen
                                "Merge-Commit" importiert werden, so das im Log der andere Branch deutlich sichtbar ist
                                ("no-fast-forward").

    git ls-files -u                     Zeigt die "unmerged" Dateien in allen Versionen (für "git show <HASH>")

                                        $ git ls-files -u
                                        100644 8129f257ec5b9d1218cd3118c7806bebe161be57 1       datei1          <- Basis
                                        100644 dc14b598cbc78d0bddcfd1b4e5bb4e5804883363 2       datei1          <- unser Branch
                                        100644 13cb3f61f1cb7bb0d666bc9916c0544a348e3bba 3       datei1          <- der zu mergende Branch

    git diff MERGE_HEAD                 Variante von "git diff HEAD" aber für den zu mergenden Branch
    git diff :1:datei1 :2:datei1        Vergleicht die Versionen 1 und 2 (nach "git ls-files -u" Notation) der Datei "datei1"

                                        $ git diff :2:datei1 :3:datei1
                                        diff --git a/:2:datei1 b/:3:datei1
                                        index dc14b59..13cb3f6 100644
                                        --- a/:2:datei1
                                        +++ b/:3:datei1
                                        @@ -1,2 +1,2 @@
                                         pr3
                                        -Fr 10. Jul 18:33:41 CEST 2015
                                        +Fr 10. Jul 18:34:16 CEST 2015

    git log --merge --left-right -p     Zeigt bei Merge-Konflikten die problematischen Diffs
                                        ("-p" kann auch hier nach Stichwort suchen!)

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

    git checkout MERGE_HEAD -- datei1   Gezielt "datei1" mit der Version des zu mergenden Branch überschreiben (z.B. weil sie Conflicts hatte)

    git commit                          Hier ohne Optionen committet den Merge mit vorgefertigter Meldung aus .git/MERGE_MSG
    git commit --amend                  Ersetzt das letzte Commit durch ein neues mit anderer Message ("git reset --soft HEAD^" und "git commit -c ORIG_HEAD")
    
    git merge --abort                   Resettet die Merge Operation *vor* dem Commit
    git reset --hard HEAD               Resettet die Merge Operation *vor* dem Commit
    git reset --hard ORIG_HEAD          Resettet den letzten Merge *nach* dem Commit

    git reset --soft <COMMIT>           Ändert HEAD Referenz, ignoriert Index und Arbeitsverzeichnis (zum Umschreiben von Commits)
    git reset [--mixed] <COMMIT>        Ändert HEAD Referenz und Index, ignoriert aber das Arbeitsverzeichnis (z.B. weil Kollege parallelen Commit gemacht hat)
    git reset --hard <COMMIT>           Ändert HEAD Referenz, Index und Arbeitsverzeichnis (also quasi ein sauberes "checkout"?)

    git cherry -v master HEAD           Zeigt innerhalb eines Branch an, welche Commits noch nicht im master Branch sind.
    git cherry-pick -x <COMMIT>         Erstellt neuen Commit auf Basis des angegebenen mit extra Zeile "(cherry picked from commit <HASH>)"

    git rebase <BRANCH>                 Versetzt den Anfangspunkt eines Branches (z.B. in Branch mit "master", weil dieser inzwischen schon einige Änderungen selbst eingebaut hat)
    git rebase -i <COMMITS>             Interaktives Menü zum Löschen/Ändern/Umsortieren der angegebenen Commits (z.B. "master~3" für die letzten 3 in "master")
    
Remote Repositories
-------------------
    
    git remote add <REMOTE>         Fügt neues Remote-Repository hinzu   
    git remote show [<REMOTE>]      Zeigt die bekannten entfernten Repositories

    git fetch                       Holt alle oder bestimmte Objekte und damit auch deren Historie aber ändert noch nicht an den lokalen Dateien.
    git pull                        Holt neue Änderungen aus einem anderen Repository
    git push                        Überträgt Änderungen in ein anderes Repository

Fehler suchen
-------------

    git bisect start            Erzeugt einen Internen-Branch auf Basis von HEAD mit einigen Meta-Daten für die Binary-Section.
    git bisect good [<COMMIT>]  Sagt bisect das eine bzw. die aktuelle Version funktioniert; welchselt den Standpunkt
    git bisect bad [<COMMIT>]   Sagt bisect, daß eine bzw. die aktuelle Version nicht funktioniert; wechselt den Standpunkt
    git bisect log              Zeigt alle bisher als gut oder schlecht markierten Versionen
    git bisect reset            Beendet das Bisect

    git blame <FILE>            letzte Anderungen zeigen

    git log -S<TEXT> -p         Zeigt alle commits bei denen TEXT im Diff auftaucht ("Pickaxe" Suche)

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
        "log --graph --all --full-history --pretty=format:'%Cred%h%Creset %x09 %ad %C(bold blue)<%ae>%Creset %C(yellow)%d%Creset %s' -n 15"

    * f355408        Wed Jul 8 20:43:56 2015 +0200 <cb@lathspell.de>  typo
    *   1c7ba74      Wed Jul 8 20:42:42 2015 +0200 <cb@lathspell.de>  Merge branch 'bug10'
    |\  
    | * 3cc59c3      Wed Jul 8 20:37:53 2015 +0200 <cb@lathspell.de>  (bug10) added client gitg
    | * b192e13      Wed Jul 8 19:02:10 2015 +0200 <cb@lathspell.de>  working in branach bug10
    * | fd4568d      Wed Jul 8 19:09:27 2015 +0200 <cb@lathspell.de>  cleanup in master
    |/  
    * 75d7f86        Tue Jul 7 22:08:41 2015 +0200 <cb@lathspell.de>  m

Submodule
---------

Git Submodule sind Unterverzeichnisse in die andere Projekte, wie z.B. benötigte Libraries, geklont werden.
Diese Submodule sind komplett eigenständig, sie können nicht verändert werden und teilen nicht die Historie des eigentlichen Projekts.
(Falls das nicht genau das gewünschte ist, soll man nach "git pull -s <SUBTREE>" suchen!)

Die URL der Submodule wird in .gitmodules gespeichert und ist damit selbst unter Versionskontrolle. Damit das Submodul allerdings aktiv ist,
muss die URL auch in .git/config stehen, was von "git submodule add" oder manuell auch mit "git submodule init/deinit" gemacht werden kann.

    git submodule add <GIT-URL>         Klont ein anderes Git Repository in das Arbeitsverzeichnis; erstellt .gitmodules Datei
    git submodule summary               Zeigt das Log der neuen Upstream Änderungen aller Submodule
    git submodule update [--recursive]  Updated die Submodule
    
SVN Unterstützung
-----------------
    
Git kann SVN Projekte mit "git svn ..." Befehlen importieren und als Git Verzeichnis speichern. Änderungen können dann später zum SVN Server geschickt oder von dort geholt werden.

Bei der Benutzung gibt es allerdings etliche Dinge (hier nicht aufgeführt, siehe Buch) zu beachten, u.a. sind die "git svn ..." Befehle weder identisch zu den original Git Befehlen noch zu den SVN Befehlen:

    git svn clone <SVN-URL>     Erstellt ein lokales Git Repository auf Basis der SVN URL ähnlich wie "svn checkout"
    git svn info                Zeigt die Upstream URL und Revision an
    git svn log                 Zeigt das Log im SVN Stil mit SVN Revision wie "svn log"
    git svn blame <DATEI>       Zeigt die Änderungen in einer Datei wie "svn blame"
    git add / git commit        Zum lokalen Speichern von Änderungen
    git svn dcommit             Schiebt lokale Commits zum SVN Server wie "svn commit" bzw. "git push" und zeigt neue SVN Revision an
    git svn rebase              Holt alle neuen Änderungen vom SVN Server wie "svn update" bzw. "git pull" und zeigt neue SVN Revision an

Plumbing aka interne Datenstrukturen ansehen
--------------------------------------------

    git ls-files                            Zeigt die Datein im Index an, also alle, die Git bekannt gemacht wurden.
    git ls-files --stage                    Zeigt Hash aller Dateien an

    git rev-parse <HASH|Tag>                Zeigt den kompletten Hash zu einem Hash oder einem Tag.

    zlib-flate -uncompress < <OBJ-FILE>     Datei entpacken und angucken, könnte Binär sein!
    git cat-file -t <HASH>                  Typ dieses BLOBs anzeigen
    git cat-file -p <HASH>                  Inhalt dieses BLOBs anzeigen

    git hash-object <FILE>                  Berechnet nur den aktuellen Hash
    git hash-object --write <FILE>          Erstellt BLOB Objekt und liefert Hash (Teil von "add").

    git write-tree                          Erstellt Tree Objekt der aktuellen Dateien und liefert Hash (Teil von "commit").
    git commit-tree <HASH>                  Erstellt Commit Objekt für einen Tree (Teil von "commit").


Konzepte
========

Datentypen:
-----------
* BLOB
* Tree
* Commit
* Tag

Datenbereiche:
--------------
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

Mittels der Git "Plumbing" Befehle:

	$ git cat-file -t c8c3c96e9f776d2d2e5e8fc097104818a68d8355
		commit

	$ git cat-file -p c8c3c96e9f776d2d2e5e8fc097104818a68d8355
		tree b07237be0ee6ede33983935620532aeaf581e14a
		parent fb21eb2b5c0dba041380ab8e48f837c1ba26dbc8
		author Christian Brunotte <cb@lathspell.de> 1436045859 +0200
		committer Christian Brunotte <cb@lathspell.de> 1436045859 +0200

		3

	$ git cat-file -t b07237be0ee6ede33983935620532aeaf581e14a
		tree

	$ git cat-file -p b07237be0ee6ede33983935620532aeaf581e14a
		100644 blob a01ee289f9a3c65287845c5138783d4f3b24a443    .gitignore
		100644 blob bcd048ce94690d9ea22d8972f6dbddac19140de0    README.md
	
	$ git cat-file -t a01ee289f9a3c65287845c5138783d4f3b24a443
		blob

	$ git cat-file -p a01ee289f9a3c65287845c5138783d4f3b24a443
		.*.swp

Prüfsummenberechnung:
	$ printf "blob 7\000.*.swp\n" | sha1sum 
		a01ee289f9a3c65287845c5138783d4f3b24a443  -
	$ git hash-object .gitignore
		a01ee289f9a3c65287845c5138783d4f3b24a443

   
Unterschiede zu SVN
-------------------

Central vs. Distributed
* SVN arbeitet immer mit einem zentralen Repository.
* Git hat in jedem Arbeitsverzeichnis ein vollwertiges Repository und kann das mit beliebig vielen Remote Repositories abgleichen.
* => Git kommt temporär ohne Netzwerkverbindung aus, man kann Änderungen nicht nur im Arbeitsverzeichnis machen sondern auch schon, während man noch offline ist, in passende Commits packen.
* => Da Commits offline und von mehreren Personen gleichzeitig gemacht werden können, kann Git keine fortlaufende Revisionsnummer vergeben sondern muss mit Hash-Werten als "Versionen" arbeiten
* => Git kann aus mehrere Quellen verfolgen um daraus selektiv einzelne Commits zu mergen.
* => Git Klone sind komplette Kopien aller gesamten Daten und Historie und damit auch vollständige Backups bzw. Ausgangspunkte für Forks.
* => Git kann nicht, wie SVN, nur einzelne Unterverzeichnisse auschecken (interessant bei Monster-Projekten wie z.B. dem KDE)
* => Mit Git kann man als Entwickler viele Branches und Merges erzeugen, die aber nur lokal bleiben und damit nicht das zentrale Repo vollmüllen.

Diffs vs. Object-Snapshots
* SVN arbeitet auf Basis von diffs, speichert bei Commits also immer nur die Unterschiede zur letzten Version der jeweiligen Datei.
* Git erstellt Snapshots der Dateien, speichert sie also auch bei nur kleinen Änderungen komplett ab.
* => Bei vielen kleinen Änderungen an großen Dateien braucht SVN weniger Platz mit seinen Diffs als Git mit seinen vielen Snapshots. Üblicherweise ist Git aber wesentlich sparsamer!
* => Beim Vergleich von Älteren Commits muss SVN den jeweiligen Stand immer aus den Diffs aufbauen während Git die Objekte sofort verfügbar hat und daher schneller ist.

Ewige Versionen
* SVN vergisst eine einmal committete Datei nie wieder; Commits können nicht verändert werden
* Git kann Commits und Bäume nachträglich umhängen so das es Objekte gibt die irgendwann nicht mehr referenziert und daher gelöscht werden.       

Staging Area
* SVN kennt nur das Arbeitsverzeichnis und das (zentrale) Repository.
* Git hat zwischen Arbeitsverzeichnis und (lokalem) Repository noch die Staging Area, "Index" genannt, in der Transaktionen vorbereitet werden.
* => Git ist verwirrender :-/

Branches
* SVN unterstützt Branches, sie werden traditionell aber eher selten benutzt.
* Git wurde mit dem Wunsch entwickelt, möglichst einfach mit Branches arbeiten zu können und diese selbst für Kleinigkeiten zu verwenden; hat keine grundsätzlich besseren Features nur irgendwie im Detail bequemer

Workflows
=========

Siehe https://www.atlassian.com/git/tutorials/comparing-workflows

Centralized Workflow
--------------------

Es gibt ein zentrales Repository und alle Entwickler klonen dieses. Änderungen
werden dann nur im lokalen trunk gemacht ohne überhaupt jemals zu branchen.
Fertige Änderungen werden zum Master gepushed wobei vorher mittels pull die
neusten Änderungen vom master-trunk gemerged werden. Der Workflow orientiert
sich stark daran wie traditionelle, zentrale, Systeme wie SVN benutzt werden.

Feature Branch Workflow
-----------------------

Es gibt ein zentrales Repository und alle Entwickler klonen dieses. Für Änderungen
wird jeweils lokal ein Feature-Branch erzeugt in dem dann gearbeitet wird.
Fertige Änderungen werden zusammen mit ihrem Branch veröffentlicht
(git push -u/--set-upstream) und dann erst gemerged (am besten mit --no-ff damit
der Merge sichtbar bleibt).

Gitflow Workflow
----------------

Dieser Workflow ist für große Projekte mit Releases und Hot-Fixes etc.
Hier werden im zentralen Repository mehrere Branches benutzt:
* trunk enthält nur Tags für die offiziellen Releases
* devel enthält ist der Hauptzweig zum arbeiten
* Feature-Branches zweigen von devel ab und enthalten Änderungen die dann auch nur nach devel zurück gemerged werden.
* Release-Branches zweigen ebenfalls von devel ab und enden in einem Tag, der dann in trunk gemerged wird. Das erlaubt kleine Änderungen im Release zweig während in devel schon weiter gearbeitet wird.
* Hotfix-Branches zweigen von den Release-Tags ab und enden in neuen Release-Tags die nach trunk gemerged werden.

Forking Workflow
----------------

Dieser Workflow entspricht dem Arbeiten auf Github: jeder Entwickler klont das ursprüngliche Repository, läst seines aber für alle anderen erreichbar auf einem Server liegen. Dadurch kann sein Repo jederzeit die Rolle des "offiziellen Repository" übernehmen. Dieses geklonte Repo auf dem Server wird dann wiederum lokal geklont um dort Änderungen vorzunehmen, die dann in das eigene Repo auf dem Server gepushed werden. Die Änderungen auf vom eigenen Server-Repo können dann dem Entwickler des "offiziellen" Repos per Pull-Request bekannt gemacht werden. Alternativ können Leute auch anfangen direkt das Server-Repo zu benutzen.

GUI
===

* gitg            Sehr schön um Branches zu visualisieren
* gitk
* git-cola

Server
======

* git-daemon      Ist bei Debian /usr/lib/git-core/git-daemon und hat Manpage im git-man Paket. Mit systemd und --inetd nutzbar.

Links
=====
    
* GIT Cheat Sheet - https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf

* Interaktive Cheat Sheet - http://ndpsoftware.com/git-cheatsheet.html

* Unterschiede zu SVN - https://git.wiki.kernel.org/index.php/GitSvnComparison
* Unterschiede zu SVN - http://www.codeforest.net/git-vs-svn
* Unterschiede zu SVN (Kritik: scheint "svn switch" nicht zukennen?! Nicht überzeugend.) - http://markmcb.com/2008/10/18/3-reasons-to-switch-to-git-from-subversion/

