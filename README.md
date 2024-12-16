
# Creazione di una Home Network Sicura
#### Introduzione al Progetto
Il presente progetto ha l'obiettivo di configurare una rete domestica sicura, implementando una DMZ per ospitare un server pubblico e separare i servizi critici dalla rete LAN interna. Questo ambiente è progettato per dimostrare le mie competenze nell’ambito della configurazione di firewall, gestione delle regole di accesso e utilizzo di strumenti di sicurezza come pfSense e Docker.
Le configurazioni implementate seguono il principio del minimo privilegio, limitando il traffico tra le reti e garantendo visibilità tramite il logging delle connessioni.
### Panoramica Tecnica
#### Architettura della Rete
- **LAN:** 192.168.2.0/24 (Client interni, es. Windows 11 e Kali Linux)
- **DMZ:** 192.168.3.0/24 (Server Ubuntu con Docker per i servizi pubblici)
- **WAN:** Rete esterna con accesso a Internet
#### Obiettivi:
1. Configurare pfSense come firewall principale.
2. Implementare una DMZ per isolare i servizi pubblici.
3. Configurare regole di firewall che consentano solo il traffico necessario.
4. Installare e configurare Docker su un server nella DMZ per ospitare applicazioni sicure.
---
### Configurazione di pfSense
#### Configurazione delle Interfacce
1. **LAN:**
   - Assegnata all’interfaccia 192.168.2.1/24.
   - Gateway per i dispositivi interni.
2. **DMZ:**
   - Assegnata all’interfaccia 192.168.3.1/24.
   - Isolata dalla LAN tramite regole di firewall.
3. **WAN:**
   - Configurata per ottenere un indirizzo IP dinamico dal provider ISP.
  
![Interfacce pfSense](https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/Configurazione%20interfacce%20pfSense%20per%20GIT.png)
     
#### Regole di Firewall Implementate
| **Regola**          | **Fonte**         | **Destinazione**    | **Porta/Protocollo** | **Motivazione**                                           |
|---------------------|-------------------|---------------------|----------------------|----------------------------------------------------------|
| WAN → DMZ           | WAN Subnets      | 192.168.3.10        | 80, 443             | Consentire traffico HTTP/HTTPS verso il server web DMZ. |
| DMZ → WAN           | 192.168.3.10     | Any                 | Any                 | Consentire al server DMZ di accedere a Internet.         |
| DMZ → LAN           | DMZ Subnets      | LAN Subnets         | Any                 | **Bloccata:** Isolare la DMZ dalla LAN.                 |
| LAN → WAN           | LAN Subnets      | Any                 | Any                 | Consentire il traffico verso Internet dalla LAN.         |

## Screenshot delle regole di pfSense

-   Regole WAN
<img src="https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/Regole%20WAN%20per%20GIT.png" width=450 height=250>
-  Regole DMZ
<img src="https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/REGOLE%20DMZ%20per%20GIT.png" width=450 height=250>
-   Regole LAN
<img src="https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/Regole%20LAN%20per%20GIT.png" width=450 height=250>
-  Logging attivato per le regole critiche
<img src="https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/Abilitazione%20Logging%20per%20regole%20critiche%20tagliato.png" width=950 height=100>

---
### Configurazione del Server Ubuntu nella DMZ
#### Passaggi:
1. **Installazione e configurazione base di Ubuntu Server:**
   - Scaricato e installato Ubuntu Server 22.04.
   - Configurato l’indirizzo IP statico: 192.168.3.10.
2. **Test di connettività:**
   - Eseguito `ping` verso 8.8.8.8 e altri dispositivi nella rete per verificare la connettività.
   - Risolto il problema di mancata connettività tramite regole di firewall aggiuntive.
  
## Screenshot:
#### Configurazione Ubuntu Server
![Configurazione Ubuntu Server](https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/Configurazione%20IP%20statico%20su%20Ubuntu%20per%20GIT.png)
#### Output Test
![Output Test](https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/Ubuntu%20Server%20only%20Ping.png)

---
### Configurazione di Docker sulla DMZ
1. **Installazione di Docker:**
   - Eseguito il comando `sudo apt install docker.io`.
   - Abilitato il servizio Docker all’avvio: `sudo systemctl enable docker`.
  
![Docker ps](https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/docker_ps.png)
     
2. **Esecuzione di un container di prova:**
   - Eseguito un server web di prova:
     ```bash
     sudo docker run -d -p 80:80 --name test-web nginx
     ```
![Server di prova](https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/docker_server_nginx.png)

   - Verificato l’accesso al server web tramite browser da un dispositivo nella rete LAN.

![Accesso a Server Web da rete interna](https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/schermata_nginx_da_kali.png)
     
---
### Verifica e Test
1. **Verifica delle regole di firewall:**
   - Eseguito traffico di prova (es. HTTP/HTTPS) per confermare il funzionamento delle regole implementate.
  
![Log regole pfSense](https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/Log%20regole.png)


2. **Test del server nella DMZ:**
   - Accesso al server web da Internet.
![Accesso al Server dal WEB](https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/nginx_web_android%20tagliata.jpg)


   - Conferma che il traffico dalla DMZ è bloccato verso la LAN.
  
   ![Creazione server temporaneo dall'interno della LAN](https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/Possibilit%C3%A0%20di%20accesso%20a%20rete%20lan.png)

   ![Tentativo di connessione non riscito dalla DMZ alla LAN](https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/Connessione%20server%20non%20riuscita.png)
   ![LOG traffico bloccato](https://github.com/Gif-97/HomeNetwork-Pratice/blob/main/media/pic/HomeNetworkPratice%20GIT/Log_connessione_bloccata.png)
---
### Miglioramenti Futuri
1. **Integrazione di Wazuh come SIEM:**
   - Pianificato per monitorare i log di pfSense e i container Docker.
2. **Espansione della DMZ:**
   - Aggiunta di ulteriori servizi pubblici (es. server FTP o DNS) con regole di firewall dedicate.
3. **Intergrazione di un honeypot:**
   - Posizionato in una vLAN esterna al firewall e creata ad hoc.
4. **VPN:**
   - Creazione di una VPN per collegamenti sicuri alla rete da remoto.
     
### Conclusione
Questo progetto dimostra la mia capacità di configurare un ambiente di rete sicuro con strumenti open source, di risolvere problemi tecnici complessi e di documentare l’intero processo in modo professionale. Tale esperienza è applicabile direttamente in ambiti lavorativi legati alla sicurezza informatica e alla gestione delle infrastrutture.

