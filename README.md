# PgModeler_Installation
Dieses Dokument soll als Anleitung zur ordentlichen Installation von PgModeler dienen.
Man kann auch die [offizielle Dokumentation](https://www.pgmodeler.io/support/installation) verfolgen. Ich möchte allerdings auf die Fallen und Stolpersteine, welche mir aufgefallen sind, aufmerksam machen.
Diese Installation ist für die **Windows** Installation interessant.
[Hier](#TLDR) ist ein Link zum TLDR.

# Schritt für Schritt
1. Installation von [MSYS2](https://github.com/msys2/msys2-installer/releases). Dazu auf der GitHub-Seite den neuesten Release und von diesem die ".exe" Datei herunterladen.
   Zum Zeitpunkt der Anleitung ist es dies [hier](https://github.com/msys2/msys2-installer/releases/download/2025-02-21/msys2-x86_64-20250221.exe)
2. Installation von [PostGre](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads). Hier die Version 16.X herunterladen. Dies wird von PgModeler empfohlen. Es kann durchaus auch mit neueren Versionen funktionieren.
3. Ladet euch den neuesten main-Branch des PgModeler-Repositories herunter
   ```bash
   git clone https://github.com/pgmodeler/pgmodeler.git
   git checkout main
   ```
4. Öffnet in dem Repository die Datei `pgmodeler.pri` und ersetzt in den folgenden Zeilen:
   ```text
   windows {
      !defined(PGSQL_LIB, var): PGSQL_LIB = C:/msys64/mingw64/bin/libpq.dll
      !defined(PGSQL_INC, var): PGSQL_INC = C:/msys64/mingw64/include
      !defined(XML_INC, var): XML_INC = C:/msys64/mingw64/include/libxml2
      !defined(XML_LIB, var): XML_LIB = C:/msys64/mingw64/bin/libxml2-2.dll
      INCLUDEPATH += "$$PGSQL_INC" "$$XML_INC"
    }
   ```
   den jeweiligen Pfad mit eurer Installation von MSYS2. Falls Ihr diesen in eurer Installation nicht verändert habt, passen die Standardwerte.
5. Öffnen von MSYS2 MINGW64. **Hier nicht den "Standard" MSYS2 öffnen sondern EXPLIZIT nach MSYS2 MINGW64 suchen**
6. Folgende Kommandos nach und nach ausführen:
   ```bash
   ## AB HIER IN 'MSYS2 MINGW64' ##
   pacman -Suy
   pacman -Suy  <- Ganz bewusst 2mal!
   pacman -S mingw-w64-ucrt-x86_64-gcc
   pacman -S base-devel mingw-w64-x86_64-make mingw-w64-x86_64-gcc mingw-w64-x86_64-postgresql mingw-w64-x86_64-qt6
   ## Ob ihr diese Installation korrekt durchgeführt habt seht ihr, wenn ihr "qmake-qt6" ausführen könnt! ##
   ## Solltet ihr das nicht ausführen können, (zb. sagt euch MSYS2 dass der Befehl nicht gefunden wurde) dann habt ihr das flasche MSYS-Fenster offen! ##
   ```
7. Legt euch einen neuen Ordner an, in dem eure zukünftige PgModeler Installation.
   Bedenkt dabei, dass ihr mit dem Kommando keine Unterverzechnisse in neuen Ordnern anlegen könnt! Wenn ihr das machen möchtet müsst ihr jeden Ordner einzeln mit einem mkdir-Befehl anlegen.
   ```bash
   mkdir $EUER_VERZEICHNIS 
   ```
8. Navigiert in den Ordner des PgModeler Git-Repo´s und führt folgende Befehle aus:
   ```bash
   ## AB HIER IN 'MSYS2 MINGW64' ##
   cd $GIT_ORDNER

   ## Ändert hier den $INSTALLATION_ROOT in den Pfad eures in Schritt 7 angelegten neuen LEEREN Ordners ##
   qmake-qt6 -r CONFIG+=release PREFIX=$INSTALLATION_ROOT pgmodeler.pro
   make && make install
   cd $INSTALLATION_ROOT
   windeployqt-qt6 pgmodeler.exe gui.dll
   ```
9. Post-Installation
   Ich bin mir selbst nicht sicher ob diese Schritte wirklich wichtig sind, da Sie wohl mit Plugins zu tun haben. Ich habe Sie trotzdem durchgeführt und geschadet hat es nicht :)
   Bei dem 3. Befehl hat er bei mir die D3Dcompiler_47.dll nicht gefunden. Ich habe diese einfach rausgelöscht, es funktioniert trotzdem.
   ```bash
    ## Ersetzt in dieser Zeile wieder $INSTALLATION_ROOT mit eurem PgModeler-Installations (nicht Git) Verzeichnis ##
   echo -e "[Paths]\nPrefix=.\nPlugins=qtplugins\nLibraries=." > $INSTALLATION_ROOT/qt.conf

      ## Ersetzt hier $MYSYS2_ROOT durch den Pfad den ihr in Schritt 4 abgeändert habt. -> Bei keiner Änderung wäre es also /c/msys64 ##
   cd $MSYS2_ROOT/mingw64/bin/

   ## Ersetzt auch hier $INSTALLATION_ROOT mit dem Installations Verzeichnis ##
   cp D3Dcompiler_47.dll libb2-1.dll libbrotlicommon.dll libbrotlidec.dll libbz2-1.dll libcrypto-3-x64.dll libdouble-conversion.dll libfreetype-6.dll libgcc_s_seh-1.dll libglib-2.0-0.dll libgraphite2.dll libharfbuzz-0.dll libiconv-2.dll libicudt*.dll libicuin*.dll libicuuc*.dll libintl-8.dll liblzma-5.dll libmd4c.dll libpcre2-8-0.dll libpcre2-16-0.dll libpng16-16.dll libpq.dll libssl-3-x64.dll libstdc++-6.dll libwinpthread-1.dll libxml2-2.dll libzstd.dll zlib1.dll $INSTALLATION_ROOT

   ## Denkt Hier wieder daran, dass Ihr keine Unterverzeichnisse von neuen Ordnern machen könnt! ##
   mkdir $INSTALLATION_ROOT/qtplugins
   mkdir $INSTALLATION_ROOT/qtplugins/tls
   cp -r $MYSYS2_ROOT/mingw64/share/qt6/plugins/platforms/tls/* $INSTALLATION_ROOT/qtplugins/tls
   ```
10. Die Installation ist jetzt abgeschlossen!
    Ihr könnt das Programm nun entweder über
    `./pgmodeler.exe`
    oder
    Klick im Explorer auf pgmodeler.exe
    ausführen und das Programm genießen!

