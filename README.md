Questo reposito è un fork di  [iorestoacasa_monitoring](https://github.com/iorestoacasa-work/iorestoacasa_monitoring)
adattato ad un singolo server, indicato nel file `jitsi_targets.yml`
Lo script per raccogliere le metriche e i relativi file Python provengono dal progetto [iorestoacasa_addmetrics](https://github.com/iorestoacasa-work/iorestoacasa_addmetrics)
Il funzionamento logico è il seguente: 
* Lo script `add-metrics.sh` abilita le metriche di Jitsi tramite Colibri.
* Il servizio `iorestoacasa_metrics.service` avvia lo script `iorestoacasa_metrics.py` che cattura le metriche di Jitsi e le espone in un formato compatibile con Prometheus su una certa porta (8081)
* Prometheus raccoglie le metriche dai server definiti in `jitsi_targets.yml`
* Grafana mostra sulla dashboard le metriche.
