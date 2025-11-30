Datum: 30. listopadu 2025
Projekt: Paralelní Analyzátor Textových Souborů (Word Counter)
Autor: [Vaše jméno, volitelně]

=======================================================
1. POPIS PROJEKTU A CÍL
=======================================================

Tento program je implementace Producer-Consumer (Producent-Konzument) schématu v Javě pro efektivní analýzu textových souborů (např. logů).

Cílem je:
1. Zpracovat velké soubory rychleji díky paralelismu.
2. Zamezit zablokování aplikace (blokující I/O) tím, že se I/O a výpočetní práce provádí asynchronně na pozadí.
3. Spočítat frekvenci výskytu každého slova v souboru.

=======================================================
2. ARCHITEKTURA A SOUBĚŽNOST
=======================================================

Projekt je rozdělen do rolí pro řešení problémů souběžnosti:

A) TŘÍDA WORDCOUNTSERVICE (Orchestrátor):
   Řídí celý proces, inicializuje zdroje a vlákna. Obsahuje hlavní logiku.

B) TŘÍDA FILEREADERTASK (PRODUCENT):
   Čte řádky ze souboru a vkládá je do fronty.

C) TŘÍDA WORDANALYZERWORKER (KONZUMENTI):
   Pool 4 vláken (Workerů) odebírá řádky z fronty, zpracovává je a aktualizuje výsledky.

D) TŘÍDA INPUTVALIDATOR:
   Stará se o získání a validaci platné cesty k souboru z konzole.

=======================================================
3. ŘEŠENÍ SYNCHRONIZACE A KONFLIKTŮ
=======================================================

Projekt využívá speciální mechanismy pro zajištění bezpečnosti vláken:

1. BLOKUJÍCÍ FRONTA (BlockingQueue):
   - Funkce: Komunikace a koordinace mezi Producentem a Konzumenty.
   - Výhoda: Zajišťuje, že Workeři čekají, když není co zpracovávat, čímž šetří CPU (prevence Spin-lock).

2. CONCURRENTHASHMAP.MERGE():
   - Funkce: Bezpečné sčítání výskytů slov (agregace výsledků).
   - Řeší: Konflikt o zdroje (Race Condition) – zaručuje, že aktualizace počtu slova je provedena atomicky (jako jediná nedělitelná operace).

3. POISON PILL:
   - Funkce: Bezpečné ukončení (koordinace).
   - Řeší: Producent vloží speciální signál, který Workeři detekují a koordinovaně se ukončí.

=======================================================
4. POUŽITÍ A SPUŠTĚNÍ
=======================================================

Před spuštěním zkompilujte všechny .java soubory:
> javac *.java

Spuštění programu:
> java MainApp

Interakce:
Program vás vyzve k zadání cesty k souboru. Program se neukončí při chybné cestě, ale bude vyžadovat opakované zadání (díky třídě InputValidator).

Příklad vstupu:
C:\cesta\k\vasemu\logu.txt
