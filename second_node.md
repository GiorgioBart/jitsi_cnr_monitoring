# Mini guida per aggiungere un nodo a jitsi
Con la nuova versione si passa ad una configurazione MUC (multi user chat) in cui i videobridge non sono componenti di Prosody ma sono client. 
Il vantaggio è notevole in quanto, diversamente da prima, non occorre modificare e riavviare i servizi Prosody e Jicofo aggiungendo un videobridge ma è sufficiente deployare una nuova macchina, installare il solo videobridge(2) puntarlo al nodo "master" (quello con jicofo e prosody per intenderci) e jicofo lo aggiungerà tra le risorse.

I videobridge comunicano ogni tot le proprie metriche e Jicofo decide dove creare le nuove conferenze.
ATTENZIONE: ogni conferenza esiste SOLO su UN videobridge. Questo vuol dire che tutti i partecipanti di quella conferenza sono su un solo nodo.
## Esempio di HA
### Lato server
ipotizziamo che sul vdb01 ci sia una conferenza con N partecipanti. Al tempo t0 vdb01 muore (o almeno viene spento il servizio). Jicofo ne avvertirà l'assenza sulla MUC e passerà l'intera conferenza sul vdb02.
### Lato client 
I client verranno disconnessi e apparirà un messaggio di prossima riconnessione con un contatore e una barra di avanzamento. 
Passati alcuni secondi (circa 20) il client viene riconnesso e, uno alla volta, ricompaiono tutti i video dei partecipanti.
ATTENZIONE: non è immediata ma converge.

## configurazione del nodo MASTER

Nel file `/etc/jitsi/videobridge/sip-communicator.properties`
```
org.jitsi.videobridge.DISABLE_TCP_HARVESTER=true
org.jitsi.videobridge.ENABLE_STATISTICS=true
org.jitsi.videobridge.STATISTICS_TRANSPORT=muc
org.jitsi.videobridge.xmpp.user.shard.HOSTNAME=jitsi-vdb01.cedrc.cnr.it
org.jitsi.videobridge.xmpp.user.shard.DOMAIN=auth.jitsi-vdb01.cedrc.cnr.it
org.jitsi.videobridge.xmpp.user.shard.USERNAME=jvb
org.jitsi.videobridge.xmpp.user.shard.PASSWORD=password1
org.jitsi.videobridge.xmpp.user.shard.MUC_JIDS=JvbBrewery@internal.auth.jitsi-vdb01.cedrc.cnr.it
org.jitsi.videobridge.xmpp.user.shard.MUC_NICKNAME=vdb01
org.jitsi.videobridge.xmpp.user.shard.DISABLE_CERTIFICATE_VERIFICATION=true
```
Nel file `/etc/jitsi/videobridge/config`
```
# sets the XMPP domain (default: none)
JVB_HOSTNAME=jitsi-vdb01.cedrc.cnr.it
# sets the hostname of the XMPP server (default: domain if set, localhost otherwise)
JVB_HOST=
# sets the port of the XMPP server (default: 5275)
JVB_PORT=5347
# sets the shared secret used to authenticate to the XMPP server
JVB_SECRET=password1
```
## configurazione di OGNI altro nodo videobridge
Nel file `/etc/jitsi/videobridge/sip-communicator.properties`
```
org.jitsi.videobridge.DISABLE_TCP_HARVESTER=true
org.jitsi.videobridge.ENABLE_STATISTICS=true
org.jitsi.videobridge.STATISTICS_TRANSPORT=muc
org.jitsi.videobridge.xmpp.user.shard.HOSTNAME= IP del "master"
org.jitsi.videobridge.xmpp.user.shard.DOMAIN=auth.jitsi-vdb01.cedrc.cnr.it
org.jitsi.videobridge.xmpp.user.shard.USERNAME=jvb
org.jitsi.videobridge.xmpp.user.shard.PASSWORD=password1 (la stessa di prima)
org.jitsi.videobridge.xmpp.user.shard.MUC_JIDS=JvbBrewery@internal.auth.jitsi-vdb01.cedrc.cnr.it
org.jitsi.videobridge.xmpp.user.shard.MUC_NICKNAME=vdb02
org.jitsi.videobridge.xmpp.user.shard.DISABLE_CERTIFICATE_VERIFICATION=true
```
Nel file `/etc/jitsi/videobridge/config`
```
# sets the XMPP domain (default: none)
JVB_HOSTNAME=jitsi-vdb02.cedrc.cnr.it
# sets the hostname of the XMPP server (default: domain if set, localhost otherwise)
JVB_HOST=
# sets the port of the XMPP server (default: 5275)
JVB_PORT=5347
# sets the shared secret used to authenticate to the XMPP server
JVB_SECRET=password1
```
