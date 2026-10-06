---
layout: post
title: "Alles in Git – nur das Geld nicht. Bis jetzt."
date: 2026-10-06 10:30:00
categories: prozesse
tags: git, rechnungen, ki, make-or-buy, prozesse
image: /assets/article_images/2026-10-06-alles-in-git/header.png
author_name: Viktor Gottfried
author_link: /authors/viktor-gottfried
author_image: /assets/images/authors/viktor-gottfried/thumbnail.jpg
---

Eine alte Rechnung hat uns vor einer falschen neuen Rechnung bewahrt.

Sie enthielt 24 Monate Wartung in einer einzigen Position. Beim Übertragen des historischen Belegs in unser neues System hätte unsere erste Regel daraus 400 € gemacht. Auf der echten Rechnung standen 9.600 €.

Die Rechnung war nicht falsch gerechnet. Die Regel war falsch gedacht. Sie war in sich stimmig – und sie kannte unseren Geschäftsfall nicht.

Gefunden hat das keine KI und keine automatische Prüfung. Es fiel einem Menschen auf, der den echten Beleg eingab.

Das war der entscheidende Moment. Die Frage lautete nicht mehr: Wie automatisieren wir Rechnungen? Sondern: Welche Entscheidungen darf ein System treffen? Wo muss ein Mensch verantwortlich bleiben?

Gleichzeitig zeigte der Beleg ein zweites Problem. Die Informationen, um eine Rechnung richtig zu verstehen, lagen an mehreren Orten: im Angebot, in der Zeiterfassung, bei der Bank und in einer Tabelle, die jemand nebenbei pflegte. Genau dort begann unser eigentliches Projekt.

# Die Tabelle neben der Buchhaltung

Wer Projekte in Teilen abrechnet, kennt die Situation. Ein Angebot hat mehrere Positionen. Diesen Monat sind Position 1 und 3 fertig. Von Position 2 wird erst später ein Teil berechnet, der Rest noch später. Die Rechnung selbst ist dabei nicht das Schwierige. Schwierig ist die verlässliche Antwort auf eine einfache Frage: Was ist bei jeder Position schon abgerechnet – und was noch offen?

Unsere bisherige Buchhaltungssoftware konnte Teilrechnungen als Prozentsatz oder Festbetrag für das ganze Angebot erstellen. „Heute Position 1 und 3“ ging nicht. Also schrieben wir freie Rechnungen und führten eine Tabelle daneben.

Auch die Stunden aus der Zeiterfassung wurden monatlich von Hand übertragen. Zahlungseingänge ordneten wir Rechnungen zu. Und obwohl Protokolle und Anforderungen längst sauber abgelegt waren, führte von ihnen kein Weg zu Angebot und Rechnung.

Die Tabelle war damit nicht nur eine Liste. Sie war der fehlende Zusammenhang zwischen Verkauf, Leistung und Geld. Mit jeder Teilrechnung musste sie wieder stimmen. Und wenn sie nicht stimmte, war die Rechenoperation vielleicht korrekt – die geschäftliche Aussage aber falsch.

![Vorher: Die Geschäftsfakten liegen getrennt, verbunden nur durch Handarbeit](/assets/article_images/2026-10-06-alles-in-git/vorher.png)

Ein ERP-System kann solche Zusammenhänge abbilden. Für ein kleines Dienstleistungsunternehmen wäre das bei uns ein größeres Projekt geworden als die Lücke selbst.

# Standard kaufen, den eigenen Prozess bauen

Unsere Entscheidung war deshalb schlicht: Standardsoftware für Standardarbeit. Den eigenen Prozess bauen wir selbst.

Qonto bleibt die Bank. Dort entsteht das Rechnungsdokument, dort wird es finalisiert, dort bekommt es seine Rechnungsnummer. Kimai bleibt die Zeiterfassung. swops ist die schmale Verbindung dazwischen. Es hält fest, was verkauft, geliefert, berechnet und bezahlt ist – pro Position und mit dem Weg dorthin.

swops ersetzt weder die Bank noch die Buchhaltung. Es ist auch kein ERP. Sein Job ist enger: die Geschäftsfakten zusammenzuführen, die unser Prozess braucht und die kein einzelnes Standardwerkzeug für uns kennt.

Die Bank kennt das Rechnungsdokument. Die Zeiterfassung kennt die Stunden. Keine von beiden kennt von sich aus die Geschichte einer Angebotsposition über mehrere Teilrechnungen hinweg. Genau diese Geschichte ist unser eigener Prozess – und deshalb gehört sie in die Verbindung zwischen den Werkzeugen, nicht als Sonderwunsch in eines davon.

Diese Fakten liegen bei uns in Git, einem System, das jede Änderung mit Autor, Zeitpunkt und Begründung festhält. Dort lagen schon die Gesprächsprotokolle und Anforderungen. Jetzt liegt dort auch der Zusammenhang zum Geld.

> Heute geht bei uns keine Rechnung mehr an swops vorbei.

# Eine Rechnung darf nicht einfach durchlaufen

Vertrauen entsteht nicht, weil ein System eine Rechnung erzeugen kann. Vertrauen entsteht, wenn es eine falsche Rechnung stoppt.

Eine Rechnung beginnt deshalb als Vorschlag. swops prüft ihn gegen die vereinbarten Positionen und den Leistungszeitraum: Es darf nicht mehr berechnet werden, als vereinbart wurde. Wartungszeiträume und erfasste Stunden dürfen nicht zweimal auf einer Rechnung landen. Danach entscheidet ein Mensch, ob die Rechnung freigegeben wird.

Erst dann entsteht in Qonto ein Entwurf. Dessen Summen vergleichen wir noch einmal mit den Summen aus swops. Stimmen sie nicht überein, geht es nicht weiter. Den letzten Schritt erledigt bewusst kein Automatismus: Eine Person finalisiert die Rechnung in Qonto. Erst dort wird sie endgültig und bekommt ihre Rechnungsnummer.

Der Ablauf ist absichtlich keine Gerade ohne Halt. An den Stellen, an denen wirtschaftliche Verantwortung entsteht, steht ein Mensch.

![Zielbild: Der Weg von vereinbarten Positionen bis zur finalisierten Rechnung mit drei menschlichen Freigaben](/assets/article_images/2026-10-06-alles-in-git/zielbild.png)

# KI hat den Bau beschleunigt. Die Regeln kamen aus der Praxis.

Zwei Entwickler haben swops in vier Wochen neben ihrer eigentlichen Arbeit gebaut. Claude Code half beim Programmieren. Jede neue Fähigkeit begann mit einer schriftlichen Beschreibung und einem Plan; die KI setzte ihn um, wir prüften das Ergebnis.

Das war schnell. Es war aber nicht der schwierige Teil.

Schon am zweiten Tag haben wir alte Angebote und Rechnungen eingepflegt. Die erste Fassung unserer Regeln und Datenstruktur musste danach an mehreren Stellen geändert werden. Die Rechnung mit den 24 Wartungsmonaten ist das beste Beispiel: Die KI konnte die Regel sauber umsetzen. Nur ein echter Beleg und ein Mensch mit Verständnis für das Geschäft konnten zeigen, dass die Regel nicht stimmt.

Auch bei den anderen Fragen gab es nichts zu raten: Wie wird auf den Cent gerundet? Was muss auf der Rechnung stehen? Wer vergibt die Rechnungsnummer? Solche Entscheidungen muss ein Unternehmen treffen und nachvollziehbar festhalten. KI ist schnell, wenn die Regeln klar sind. Sie ersetzt nicht die Arbeit, sie richtig zu bestimmen.

# Aus einer Lücke wurde ein zusammenhängender Prozess

Die erste zuverlässige Rechnung war kein Endpunkt. Sobald die Daten verbunden waren, ergab der nächste Schritt jeweils Sinn.

Zuerst verbanden wir Anforderungen mit ihrer Quelle, dem Angebot und den Entwicklungsaufgaben, die sie umsetzen. Damit lässt sich vom Gespräch bis zur abgerechneten Position zurückverfolgen, warum etwas verkauft wurde und wo die Umsetzung steht.

Dann kamen die Stunden: Für Kunden, die nach Zeit abrechnen, bestätigt ein Mensch zum Monatsanfang die vollständig erfassten Stunden in Kimai. Daraus bereitet swops einen Rechnungsvorschlag vor. Später folgten die Zahlungen. Die Bank ordnet sie ihren Rechnungen zu; swops übernimmt diese Zuordnung auf Knopfdruck in den Zusammenhang, der auch das Angebot kennt.

Dasselbe Muster hilft am Anfang der Kette. Aus Gesprächsprotokollen, E-Mails oder Dokumenten schlägt ein KI-Assistent Anforderungen mit Verweis auf die jeweilige Quelle vor. Ein Mensch übernimmt sie. Kolleginnen und Kollegen arbeiten dafür über eine Oberfläche und Desktop-App, nicht über die Kommandozeile.

![Der Weg: Eine Lücke wird zu verbundenen Daten, dann zu Rechnungen, Stunden, Zahlungen und Anforderungen](/assets/article_images/2026-10-06-alles-in-git/weg.png)

Es sind keine vier losen Zusatzfunktionen. Sie nutzen denselben Zusammenhang: Was wurde besprochen? Was wurde verkauft? Was wurde geleistet? Was wurde berechnet? Was wurde bezahlt?

# Welche Tabelle liegt bei Ihnen daneben?

Vielleicht ist es eine Tabelle neben der Buchhaltung. Vielleicht ein Ordner mit Angeboten, aus denen niemand sicher sagen kann, was noch offen ist. Vielleicht werden Stunden, Zahlungen oder Übergaben zwischen zwei Systemen immer wieder abgetippt.

Diese Tabelle ist nicht bloß eine Notlösung. Sie zeigt, wo Ihr eigener Prozess endet und die Standardsoftware nicht mehr weiterweiß.

Früher blieb dann oft nur die Wahl: mit der Lücke leben oder ein ERP einführen. Wir haben einen dritten Weg gewählt. Die erste Rechnung lief acht Tage nach der ersten Zeile Code über swops. Nach vier Wochen stand die Verbindung, gebaut nebenher von zwei erfahrenen Entwicklern.

Wenn Sie Ihre eigene Lücke schließen wollen, schreiben Sie uns über [unsere Website](https://swcode.io/#kontakt) oder direkt unter dem LinkedIn-Beitrag zu diesem Artikel.
