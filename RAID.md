# RAID - Domande e risposte

## Cos'è il RAID? Quali livelli ci sono?

Il RAID è un meccanismo di *business continuity*, ovvero permette alle macchine di non fermarsi in caso di guasto. **Non è un'alternativa al backup**, perché non protegge da guasti estesi (più di un certo numero di dischi), da guasti logici (software) o da errori di sysadmin o utenti che hanno accesso ai dati.

Un sistema RAID si compone di un controller e due o più dischi. Se non conosci il RAID o hai dei dubbi, consulta prima la
pagina di Wikipedia a riguardo: [https://it.wikipedia.org/wiki/RAID](https://it.wikipedia.org/wiki/RAID)

## Qual è il livello RAID migliore per dischi normali (non-SSD)?

Dipende dai casi. In primis, gli unici livelli da considerare sono:
* RAID 1
* RAID 5 (o 6)
* RAID 10

Gli altri, per motivi di resistenza ai guasti o di spreco di risorse, non sono interessanti.

In presenza di dischi **piccoli** (eg. più o meno fino al tier dei 500GB) il RAID 5 è un'ottimo livello _general purposes_, ovvero adatto ad usi generici. Questo perché il rapporto tra la protezione da guasti e lo spazio disponibile è ottimo.

In presenza di dischi **medio/grandi** (1TB o più) il RAID 5 **è pericoloso**, perché la probabilità che un altro disco del RAID si rompa durante il resync dell'array aumenta al crescere della durata (che cresce in base alla dimensione dei dischi), e con dimensioni maggiori di qualche terabyte la probabilità è talmente alta da vanificare l'uso del RAID. **In questi casi le uniche opzioni sono RAID 1 o RAID 10**, a seconda delle performance in lettura, livelli di protezione desiderati e numero di dischi disponibili.

## Qual è il livello RAID migliore per dischi SSD?

Data l'enorme velocità di un disco SSD, in genere si preferisce tendere verso un RAID 1 o 10. Bisogna ricordarsi che i RAID tendono ad amplificare il numero di scritture, ed i dischi SSD hanno un numero di scritture limitate. Dunque, a livello industriale è necessario utilizzare dischi SSD costruiti appositamente per lo storage, con un numero di scritture enormemente più alto della media.

## Disco hot-spare? Si o no?

**E' altamente consigliato**. Il disco __hot-spare__ serve perché il tempo che intercorre tra la rottura del disco e l'arrivo del disco nuovo potrebbe essere fatale per l'array. Un disco __hot-spare__ entra in funzione subito, permette all'array di sincronizzarsi e rimanere nello stato di "pericolo" per il solo tempo necessario al resync (tempo che comunque va speso), mentre il nuovo disco può arrivare con tutta calma e diventare il nuovo __hot-spare__.

I casi in cui non è necessario sono pochi, ed in genere sono casi in cui un restore dai backup conviene di più che avere un disco inutilizzato (probabilmente per server casalinghi).

## Che dire invece del RAID 0?

Non è un vero livello di RAID. **Non assicura nessuna protezione**. E' perfetto se si vogliono **perdere i dati**. Ha senso solo in configurazioni complesse (vedi RAID10).

## Che dire invece del RAID 0+1 ?

Il RAID 0+1 ha probabilità di rottura maggiore del RAID 10. Inoltre, il RAID 0+1 obbliga la costruzione di un intero ramo (eg. aggiunta dell'interno RAID 0 come blocco al RAID 1), mentre invece l'alternativa RAID 10 permette di aggiungere dischi nei singoli punti senza distruggere e ricreare l'array.

Esempio: supponiamo di avere 4 dischi e voler comparare le probabilità di rottura. Abbiamo:
* RAID 0+1: si crea un RAID 1 su questi 2 set di dischi:
   * 1+2: RAID 0
   * 3+4: RAID 0
* RAID 10: si crea un RAID 0 (stripe) su questi set di dischi:
   * 1+2: RAID 1
   * 3+4: RAID 1

Entrambe le configurazioni resistono a guasti di un solo disco senza problemi. Ma qual è la probabilità che la rottura di un secondo disco porti ad una perdita dati?
* RAID 0+1: possibili configurazioni di rottura per 2 dischi:
  * 1+2: nessun dato perso (3+4 è un mirror)
  * 1+3: dati persi (dischi rotti in entrambi i lati del mirror)
  * 1+4: dati persi
  * 2+3: dati persi
  * 2+4: dati persi
  * 3+4: nessun dato perso (1+2 è un mirror)
* RAID 10:
  * 1+2: dati persi (dischi rotti nella prima metà del RAID 0)
  * 1+3: nessun dato perso (entrambi i dischi sono ridondati)
  * 1+4: nessun dato perso (entrambi i dischi sono ridondati)
  * 2+3: nessun dato perso (entrambi i dischi sono ridondati)
  * 2+4: nessun dato perso (entrambi i dischi sono ridondati)
  * 3+4: dati persi (dischi rotti nella seconda metà del RAID 0)

Dunque con il RAID 0+1 la probabilità che una seconda rottura porti alla perdita dati è del 66% (2 possibili casi di rottura su 6), mentre con il RAID 10 abbiamo il 33% di probabilità di rottura. **Questo divario aumenta all'aumentare del numero di dischi, dunque il RAID 0+1 è sempre più pericoloso.**

# Link utili

* Calcolatore RAID: http://www.raid-calculator.com/
* Confronta lo spazio usato/disponibile nei vari livelli di RAID: https://www.synology.com/it-it/support/RAID_calculator

# Riferimenti

* https://www.reddit.com/r/sysadmin/comments/6pw36x/til_why_we_should_stop_using_raid_5/
* https://www.zdnet.com/article/raidfail-dont-use-raid-5-on-small-arrays/
* https://serverfault.com/questions/513909/what-are-the-main-points-to-avoid-raid5-with-ssd
