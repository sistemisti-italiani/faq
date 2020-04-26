# Windows

## Problemi di rete comuni

### Triangolo giallo sopra il simbolo della connessione di rete (per 30 secondi, poi scompare)

* Lo switch dove hai collegato il PC ha lo [Spanning Tree Protocol (STP)](https://en.wikipedia.org/wiki/Spanning_Tree_Protocol) abilitato?
  * Se si, le porte __edge__ (ie. dove sono connessi PC/stampanti/etc) sono configurate come _PortFast_ (in STP)?
    * STP ha un _grace-period_ di un certo numero di secondi (30 per default) in cui la porta si trova in uno stato di "learning" mode, ovvero non fa passare le trame. _PortFast_ è la configurazione che cambia questo comportamento e permette il _forward_ delle trame attraverso la porta. **Da configurare solo nelle porte _edge_!**
  * E' abilitato il _Rapid Spanning Tree Protocol__?
    * _Rapid STP_ permette il forward delle trame immediatamente (al contrario di STP dove c'è un _grace-period_)
* Il livello fisico (ie. i LED che lampeggiano) si accendono immediatamente?
* Il DHCP sta funzionando correttamente?
  * Se il DHCP server non è configurato come _authoritative_ allora risponde solo se non c'è la risposta di un _authoritative_ DHCP.
  * Se non ci sono più indirizzi liberi nel _pool_ di indirizzi configurati nel DHCP, il server DHCP non risponde ai client
* Il DNS sta funzionando correttamente?
* Il router sta funzionando correttamente?
* Un firewall nella rete potrebbe bloccare l'accesso ad internet, controlla i suoi log
