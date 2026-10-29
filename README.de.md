# Reverse-Engineering-Lab für den Unterricht: Programme mit gdb, radare2, strace und ltrace lesen

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-reverse-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-reverse-lab/actions/workflows/check.yml)

Ein fertiges **Reverse-Engineering-Lab**, mit dem Sie zeigen, wie man ein Programm ohne Quellcode liest. Es stammt von der [MitsuruTech Learning Platform](https://mitsurutech.co.uk/de/learning), auf der Schulen und Hochschulen jedem Lernenden eigene Maschinen und ein Terminal im Browser geben. Jede Schülerin und jeder Schüler bekommt eine **VM** mit Ubuntu, kostenlos auszuprobieren, mit Compiler, **gdb**, **radare2**, binutils, **strace** und **ltrace** sowie fünf kleinen, für das Lab geschriebenen Programmen, die je einen Schlüssel verbergen. Die Lernenden finden die Schlüssel, indem sie die Programme untersuchen, wie es ein Verteidiger oder eine Compilerbau-Studentin täte, und tragen sie in eine Datei ein. Auf dem Rechner der Lernenden wird nichts installiert.

Es geht allein ums Lesen und Verstehen, an Programmen, die das Lab selbst kompiliert. Es lehrt weder Schadsoftware noch das Umgehen der Lizenz echter Software, weder Schlüsselgeneratoren noch das Verstecken eines Programms vor einem Debugger, weder Exploits noch das Untersuchen von Programmen, die nicht zum Lab gehören.

## Was die Lernenden tun

Das Lab legt fünf Programme in `~/revlab` ab (`hello`, das keinen Schlüssel hat und zum Üben dient, dann `crack1` bis `crack5`), dazu eine `answers.txt` und eine `README.md` im Home-Verzeichnis, die sagt, was zu tun ist, und keine Antwort verrät. Jedes Programm gibt nur `Access granted.` oder `Access denied.` aus:

1. `crack1` nimmt ein Passwort auf der Kommandozeile: ansehen, ohne es auszuführen (`file`, `strings`, `objdump`, `r2`).
2. `crack2` nimmt ein Passwort auf der Kommandozeile: in `gdb` während der Ausführung anhalten und im Speicher nachsehen.
3. `crack3` nimmt eine Zahl: die Rechnung im Disassembly lesen und rückwärts auflösen.
4. `crack4` fragt nach dem Start nach einem Passwort: seine Bibliotheksaufrufe mit `ltrace` beobachten.
5. `crack5` sucht eine Datei: seine Systemaufrufe mit `strace` beobachten.
6. Jeden Schlüssel in `answers.txt` eintragen, eine Zeile je Programm, in der Form `crack1=der-schluessel`.

Dahinter steht die Lehre, die ein Verteidiger braucht: Alles, was ein Programm wissen muss, um zu funktionieren, kann ein Leser finden. Ein Geheimnis, das in einem Programm steckt, ist also keines.

## Auf jeder Kopie ein anderer Schlüssel

Das Repository ist öffentlich, deshalb steht kein Schlüssel darin. Beim ersten Start der VM erzeugt `setup` neue Zufallsschlüssel, legt sie in einem Ordner ab, den nur root betreten kann, baut die Programme damit und löscht die Quellen. Die Schlüssel sind zufällig, keine eingepflanzten Festwerte. Die Antworten werden mit den Schlüsseln der eigenen Kopie verglichen, die Antworten einer Mitschülerin oder die Dateien des Labs verraten daher nichts.

## So wird bewertet

Das Lab hat eine **Prüfung**, ein Skript, das eine abgegebene VM bewertet, ohne dass Sie sie lesen müssen. Es läuft auf einer Kopie der gespeicherten Maschine nach einem frischen Start und liest die `answers.txt` im Home-Verzeichnis. Jeder der fünf Schlüssel muss der Schlüssel sein, der für diese Kopie dieses Programms erzeugt wurde. Eine Vermutung, eine leere Zeile, der Schlüssel einer anderen Kopie oder ein Schlüssel beim falschen Programm besteht nicht. Die Prüfung führt außerdem jedes Programm mit seinem eigenen Schlüssel aus, sodass ein verändertes Programm auffällt. Die erste ausgegebene Zeile sagt, was nicht stimmt.

Was sie nicht tut: Sie bewertet die Antworten und nicht den Weg dorthin, und wer auf der eigenen VM `sudo` benutzt, könnte die gespeicherten Schlüssel lesen. Das Lab vertraut den Lernenden ihre eigene Maschine an. Es ist eine Prüfung für den Unterricht, kein Beweis.

## Was in der Datei steht

`reverse-lab.lab.yaml` ist eine vollständige **VM-Lab-Definition**, genau das, was die Plattform ausführt:

| Schlüssel | Was es ist |
| --- | --- |
| `cpus`, `memory`, `disk` | die Größe der VM jeder Person: 1 CPU, 1024 MB und 10 GB |
| `open` | die Ports, die sich im Browser öffnen: keine, das Lab ist das Terminal |
| `setup` | ein Skript, das einmal als root läuft, wenn die VM jeder Person zum ersten Mal startet: es installiert Compiler und Werkzeuge aus dem Ubuntu-Archiv, erzeugt die Schlüssel, baut die fünf Programme und schreibt `answers.txt` und die README |
| `check` | das Bewertungsskript, als root auf einer Kopie der gespeicherten Platte nach einem frischen Start ausgeführt; sein Exit-Status ist das Ergebnis, `0` bedeutet bestanden |

## Auf der Plattform verwenden

**So geben Sie es einer Klasse als Aufgabe:**

1. Fügen Sie in Ihrem Modul eine Aufgabe hinzu und wählen Sie die Karte **Ubuntu-VM-Lab**.
2. Drücken Sie das Beispiel Reverse Engineering oder fügen Sie den Inhalt von `reverse-lab.lab.yaml` ein. Ein Beispiel ersetzt den Text, und Rückgängig bringt Ihren zurück.
3. Veröffentlichen Sie und probieren Sie zuerst Ihre eigene Kopie aus. Jede Person hat eine eigene VM, die zwischen den Sitzungen erhalten bleibt, mit eigenen Schlüsseln.
4. Weisen Sie es wie jede Aufgabe zu. Nach der Abgabe führen Sie die Prüfung in der Zeile einer Person in der Klassenliste aus: Sie startet eine Kopie der gespeicherten Maschine, die nicht zurückgespeichert wird, und zeigt, ob sie bestanden hat und was sie ausgegeben hat.

Eine Basics-Lektion der Plattform lässt sich in diesem Lab bearbeiten. Die [Seite zu diesem Lab](https://mitsurutech.co.uk/de/help/lab-reverse-lab) im Handbuch, der [Leitfaden zu Ubuntu-VM-Labs](https://mitsurutech.co.uk/de/help/labs) und die Seite mit den [Beispiel-Labs](https://mitsurutech.co.uk/de/help/sample-labs) erklären den Rest.

Noch kein Konto für Ihre Einrichtung? [Das erste Jahr ist kostenlos](https://mitsurutech.co.uk/de/pricing) für bis zu 50 Lernende, und die [Registrierung](https://mitsurutech.co.uk/de/signup) braucht nur eine E-Mail-Adresse.

## Die Datei prüfen

Bei jedem Push prüft `.github/workflows/check.yml`, dass die Datei gültiges YAML mit den oben genannten Schlüsseln ist und dass `setup` und `check` je innerhalb der 16-KB-Grenze der Plattform liegen. Es startet keine VM: Das geschieht auf den Maschinen der Plattform.

## Was in einem Lab nicht funktioniert

Die VM erreicht das Paketarchiv von Ubuntu, PyPI, npm und GitHub, sonst nichts. Dieses Lab braucht nur das Archiv, und die Kopie einer Person kann nicht ins Internet, um ein anderes Programm zu holen. Die Einrichtung dauert beim ersten Mal ein paar Minuten, während die Werkzeuge installiert werden.

## Über uns

Entwickelt von [Mitsuru Technologies](https://mitsurutech.co.uk/de) für die [MitsuruTech Learning Platform](https://mitsurutech.co.uk/de/learning): Python, Java, Go, Node, SQL und Linux im Browser für Schulen und Hochschulen, mit Container-Labs, VM-Labs, aus GitHub bereitgestellten Web-Apps und Quizzen, die sich selbst bewerten. Code ohne Konto ausprobieren im [Playground](https://mitsurutech.co.uk/de/playground).

Weitere Labs vom selben Anbieter: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea), [lab-sample](https://github.com/mitsurutech/lab-sample), [lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab), [lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab), [lab-hdl-lab](https://github.com/mitsurutech/lab-hdl-lab), [lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab), [lab-tofu-lab](https://github.com/mitsurutech/lab-tofu-lab), [lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles) und [lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics).

## Lizenz

Dieses Werk steht unter der Lizenz [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Sie dürfen es zum Lernen und Lehren teilen und anpassen, wenn Sie Mitsuru Technologies Ltd nennen, es nicht kommerziell nutzen und alles, was Sie daraus machen, unter derselben Lizenz weitergeben. Der vollständige Text steht in der Datei `LICENSE`.

Compiler, gdb, radare2, binutils, strace und ltrace werden beim Start einer VM aus dem Ubuntu-Archiv installiert; hier wird nichts davon verteilt.
