09.05.2026
Ich habe mein neues C# und letztes grosses Projekt in C# für diese jahr angefangen.
In diesem Projekt werde ich neue Varianten nutze, Wie Klassen. Im Projekt geht es um enwickelt eines Verwaltungssystems für Mercedes.
Ich habe erst die KLassen und den Provisorischen Code fürs Menü geschrieben:
// See https://aka.ms/new-console-template for more information


namespace Mercedes_Verwaltungssystem
{
    internal class Program
    {
        static void Main(string[] args)
        {
            MercedesVerwaltung verwaltung = new MercedesVerwaltung();
            bool running = true;

            while (running)
            {
                Console.WriteLine("=== Mercedes Verwaltungssystem ===");
                Console.WriteLine("1. Auto hinzufügen");
                Console.WriteLine("2. Alle Autos anzeigen");
                Console.WriteLine("3. Beenden");
                Console.Write("Auswahl: ");

                string eingabe = Console.ReadLine();

                switch (eingabe)
                {
                    case "1":
                        MercedesAuto auto = new MercedesAuto();

                        Console.Write("Modell: ");
                        auto.Modell = Console.ReadLine();

                        Console.Write("Baujahr: ");
                        auto.Baujahr = int.Parse(Console.ReadLine());

                        Console.Write("Farbe: ");
                        auto.Farbe = Console.ReadLine();

                        Console.Write("PS: ");
                        auto.PS = int.Parse(Console.ReadLine());

                        Console.Write("Kilometerstand: ");
                        string kmInput = Console.ReadLine();

                        int km;
                        while (!int.TryParse(kmInput, out km))
                        {
                            Console.WriteLine("Ungültige Eingabe. Bitte eine Zahl eingeben");
                            Console.WriteLine("Kilometerstand: ");
                            kmInput = Console.ReadLine().Replace(".", "").Replace("'", "");
                        }

                        auto.Kilometerstand = km;

                        Console.Write("Preis: ");
                        string preisInput = Console.ReadLine();

                        int preis;
                        while (!int.TryParse(preisInput, out preis))
                        {
                            Console.WriteLine("Ungültige Eingabe. Bitte geben sie einen preis an!");
                            Console.WriteLine("Preis; ");
                            preisInput = Console.ReadLine().Replace(".", "").Replace("'", "");
                        }

                        verwaltung.AutoHinzufügen(auto);
                        Console.WriteLine("Auto wurde hinzugefügt!");
                        break;

                    case "2":
                        verwaltung.AlleAutosAnzeigen();
                        break;

                    case "3":
                        running = false;
                        break;

                    default:
                        Console.WriteLine("Ungültige Eingabe.");
                        break;
                }

                Console.WriteLine();
            }
        }

    }
}
Die KLassen:
class MercedesAuto
{
    public string Modell;
    public int Baujahr;
    public string Farbe;
    public int PS;
    public int Kilometerstand;
    public int Preis;

    public void ZeigeInfo()
    {
    Console: Console.WriteLine(($"{Modell} ({Baujahr}) - {PS}ps, {Farbe}, {Kilometerstand}km, {Preis} CHF"));
    }
}
und
class MercedesVerwaltung
{
    //Liste aller Autos
    public List<MercedesAuto> autos = new List<MercedesAuto>();

    // Auto hinzufügen
    public void AutoHinzufügen(MercedesAuto auto)
    {
        autos.Add(auto);
    }

    //Alle Autos anzeigen
    public void AlleAutosAnzeigen()
    {
        foreach (var auto in autos)
        {
            auto.ZeigeInfo();
        }
    }
}

Insgesamt benutze ich zwei Klassen. einmal für die Verwaltung und einmal für die Autos.

10.05.2026
In diesem Vorherigen Code, bin ich noch unzufrieden, weil der Code sehr einfach einstürzen kann. In dieser Abteilung, 
möchste ich das Programm sichern und robust machen.

Ich habe damit angefangen, mit 2 Codes. Beide dieser Codes kommen im internal class, also über dem Main Code.
Der erste Code ist das keines der Eingaben leer sein darf:
        // Hier kommen die Methoden rein
        static string EingabeText(string prompt)
        {
            Console.Write(prompt);
            string input = Console.ReadLine();

            while (string.IsNullOrWhiteSpace(input))
            {
                Console.WriteLine("Eingabe darf nicht leer sein.");
                Console.Write(prompt);
                input = Console.ReadLine();
            }
            return input;

Der nächste Code ist für die Zahlen:
static int EingabeZahl(string prompt, int min = 0, int max = int.MaxValue)
{
    Console.Write(prompt);
    string input = Console.ReadLine();

    int zahl;
    while (!int.TryParse(input, out zahl) || zahl < min || zahl > max)
    {
        Console.WriteLine($"Ungültige Eingabe. Bitte eine Zahl zwischen {min} und {max} eingeben.");
        Console.Write(prompt);
        input = Console.ReadLine();
    }

    return zahl;

Ich habe mich für diese Variante entschieden, weil wir unseren Code jetzt Übersichtlicher machen können.
Case 1, können wir jetzt löschen und neu schreiben, Übersichtlicher und somit einfscher

Von so:
case "1":
    MercedesAuto auto = new MercedesAuto();

    Console.Write("Modell: ");
    auto.Modell = Console.ReadLine();

    Console.Write("Baujahr: ");
    auto.Baujahr = int.Parse(Console.ReadLine());

    Console.Write("Farbe: ");
    auto.Farbe = Console.ReadLine();

    Console.Write("PS: ");
    auto.PS = int.Parse(Console.ReadLine());

    Console.Write("Kilometerstand: ");
    string kmInput = Console.ReadLine();

    int km;
    while (!int.TryParse(kmInput, out km))
    {
        Console.WriteLine("Ungültige Eingabe. Bitte eine Zahl eingeben");
        Console.WriteLine("Kilometerstand: ");
        kmInput = Console.ReadLine().Replace(".", "").Replace("'", "");
    }

    auto.Kilometerstand = km;

    Console.Write("Preis: ");
    string preisInput = Console.ReadLine();

    int preis;
    while (!int.TryParse(preisInput, out preis))
    {
        Console.WriteLine("Ungültige Eingabe. Bitte geben sie einen preis an!");
        Console.WriteLine("Preis; ");
        preisInput = Console.ReadLine().Replace(".", "").Replace("'", "");
    }

    verwaltung.AutoHinzufügen(auto);
    Console.WriteLine("Auto wurde hinzugefügt!");
    break;

Jetzt zu so:
case "1":
    MercedesAuto auto = new MercedesAuto();

    auto.Modell = EingabeText("Modell: ");
    auto.Baujahr = EingabeZahl("Baujahr: ", 1886, 2100);
    auto.Farbe = EingabeText("Farbe: ");
    auto.PS = EingabeZahl("PS: ", 1, 2000);
    auto.Kilometerstand = EingabeZahl("Kilometerstand: ", 0);
    auto.Preis = EingabeZahl("Preis: ", 0);

    verwaltung.AutoHinzufügen(auto);
    Console.WriteLine("Auto wurde hinzugefügt!");
    break;
Im nächsten Abteil, werden wir weitere Verbesserungen und erweiterungen vornähmen.

22.05.2026
Heute wurde das Programm nochmals von Fehlern durchsucht. Weitere Idee, wie man dieses Verwaltungssystem verbessern kann wurden gesammelt. 

Das nächste mal werden wir damit anfangen diese Ideen zu realisieren.

29.05.2026
Ideensammlung für Modul-122. Ich werde nächte woche mit dem Mudelprojekt starten(das ist kein eigen Projekt).
Ich will bis ende Schuljahr noch mein Mercedesverwaltungssystem fertig stellen.
Erstellen der Webseiten-Vorlage wurde endlich fertig gestellt.

Nächste Woche wird wieder mehr Informationen kommen.

# Lern-Periode 4

- Name: David Gjorgejv
- Zeitraum: 24.04.2026 bis 26.06.2026

## Grob-Planung

### Noten
Ich stehe momentan bei einer 5 in der Informatik. Ich bin zufrieden aber nicht begeistert. Eine 5 ist die mindest Anforderung kann man sagen. Mir sind Modul Noren wichtig, die spezialisiert auf meine Orientirung gemacht ist. Natürlich verscuhe ich ich vorallem in Informatik bei jedem Modul die maximale Note zu bekommen und zu lernen. Ich möchte bis ende der 3 Jahren ein Schnitt von 5.5 schaffen.

### Veränderungen
Megr Fokus auf eigene Projekte, bei dennen ich das programmieren gut selbständig lernen kann. Projekte die gut für Praktikums sind.

### Projekte / neue Technologien
Projekte in einer anderen Sprache. Berufs wichtige Projekte.

### Generelle Ziele
Sicherer in programmieren werden.

## Tagesplanungen

### Planung 05.06.2026
3 bis 5 klar messbare Arbeitspakete.

- [x] Projektidee sammeln
- [x] Projekt anfangen
- [X] Projektdoku anfangen

Heute habe ich Projektideen für mein Powershell Projekt gesammelt. Ich bin auf die Idee gekommen ein, beim einschalten des
Computers, eine Motivations Benachrichtigung.
Ich habe mit dem Skript angefangen und dazu noch die Doku.


### Planung 12.06.2026
Abwesend

- [ ] Erstes Arbeitspaket
- [ ] ...
- [X] Viertes AP

(Heute habe ich... (50-100 Wörter))

## Lernperiode Reflexion
Abwesend

## Tagesplanungen

### Planung 19.06.2026
Ich will heute den grässten Teil meines C# Projektes Fertigschreiben. Ich will die neuen Aspekte umsätzen und testen

- [x] Fast Fertig mit dem Jetzigen Code
- [x] Umsätzen vom lätzen mal
- [X] Neue Codes testen

Heute habe ich die meiste Zeit genutzt, um mein jetztiger Code mit denn vorhandenen Idee zum grössten Teil fertig zu stellen. Danach habe ich mit kleinen extra Code die das Projekt verbessernt, angefagen, die Ideen habe ich letztes mal Aufgeschrieben. Als letztes ahbe ich das ganze getestet.


### Planung 26.06.2026
Projekt fertigstellen.

- [ ] Erstes Arbeitspaket
- [ ] ...
- [X] Viertes AP

(Heute habe ich... (50-100 Wörter))

## Lernperiode Reflexion
