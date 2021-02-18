# HA-PiNetwork-Node-Info-Tab
HomeAssistant Info Tab for the PiNetwork Node-Container

[Pi is a new Crypto Currency](https://minepi.com/white-paper) which is not yet avaiable for Trading. This repository represents a solution to get the most Information you can get out of the avaiable Node-Container for the MinePi-Node within the [MinePi-App](https://node.minepi.com) and visualize it using [Node-Red](https://nodered.org), [Home Assistant](https://www.home-assistant.io), the [Mapquest-](https://developer.mapquest.com/documentation/) and [IP-API](https://ipapi.com)

## Requirements
- Running Home Assistant Instance
- Running Pi-Network-Node-Container
- Running Node-Red instance with access to HomeAssistant www folder

### Home Assistant requirements
- Installed [HACS-Store](https://hacs.xyz)
- Installed [Mini-Graph-Card](https://github.com/mork2020/mini-graph-card)
- Installed [Fold-entity-Row](https://github.com/thomasloven/lovelace-fold-entity-row)
- Installed [Monitor_Docker](https://github.com/ualex73/monitor_docker)

## Pi-Network-Node-Container requirements
- You were selected to run the TestNet-Node-Container (pi-consensus)

## Node-Red requirements
- Installed node-red-contrib-dockerode - Package
- Installed node-red-contrib-home-assistant-websocket - Package
- Access to the Home Assistant Folder www

### Installation

## Home Assistant configuration
Add this to your `configuration.yml` and restart 
```yaml
monitor_docker:
  - name: Docker
    containers:
      - pi-consensus
    rename:
      pi-consensus: Pi Network Blockchain
    monitored_conditions:
      - version
      - cpu_percentage
      - state
      - status
      - memory
      - containers_cpu_percentage
      - containers_memory_percentage
      - containers_memory
      - network_speed_up
      - network_speed_down
      - network_total_up
      - network_total_down
```

Edit your Lovelace-Dashboard in raw Mode and add this:
```yaml
  - title: Pi Network
    path: pi-network
    icon: 'mdi:bitcoin'
    badges: []
    cards:
      - type: vertical-stack
        cards:
          - type: entities
            entities:
              - entity: sensor.pinetwork_network
                name: Netzwerk
                icon: 'mdi:help-network'
              - entity: sensor.pinetwork_state
                name: Status
                icon: 'mdi:alert-circle'
              - entity: sensor.pinetwork_startedon
                name: Gestartet am
                icon: 'mdi:calendar'
              - entity: sensor.pinetwork_fehlerrate
                name: Fehlerrate
              - entity: sensor.pinetwork_peers
                name: Verbindungen
                icon: 'mdi:connection'
              - entity: sensor.pinetwork_peerspending
                name: Verbindungen (Anstehend)
                icon: 'mdi:account-clock'
              - entity: sensor.pinetwork_protocolversion
                name: Protokol-Version
                icon: 'mdi:update'
              - entity: sensor.pinetwork_version
                name: Container Build
                icon: 'mdi:update'
            title: Allgemein
            state_color: true
          - type: 'custom:mini-graph-card'
            entities:
              - entity: sensor.pinetwork_peersingoing
            name: Eingehende Verbindungen
            show:
              labels: true
            icon: 'mdi:connection'
            hours_to_show: 6
            points_per_hour: 10
          - type: 'custom:mini-graph-card'
            entities:
              - entity: sensor.pinetwork_peersoutgoing
            name: Ausgehende Verbindungen
            show:
              labels: true
            icon: 'mdi:connection'
            hours_to_show: 6
            points_per_hour: 10
          - type: picture-elements
            title: Standorte (1h)
            elements:
              - type: state-label
                entity: sensor.pinetwork_peersimageupdatedat
                style:
                  color: black
                  background: grey
                  border-radius: 5px
                  font-size: 20px
                  font-weight: bold
                  top: 95%
                  left: 50%
            image: /local/custom_images/connected_nodes.png
      - type: vertical-stack
        cards:
          - type: entities
            entities:
              - entity: sensor.pinetwork_quorum_node
                name: Node
                icon: 'mdi:help-network'
              - entity: sensor.pinetwork_quorum_phase
                icon: 'mdi:information-variant'
                name: Aktuelle Phase
              - entity: sensor.pinetwork_quorum_nodes
                name: Anzahl verbundener Nodes
                icon: 'mdi:connection'
              - entity: sensor.pinetwork_quorum_agreed
                name: Übereinstimmungen
                icon: 'mdi:thumb-up'
              - entity: sensor.pinetwork_quorum_disagreed
                name: Abweichungen
                icon: 'mdi:thumb-down'
              - entity: sensor.pinetwork_quorum_failed
                name: Fehlerhaft ab
                icon: 'mdi:alert'
            state_color: true
            title: Quorum
          - type: 'custom:mini-graph-card'
            entities:
              - entity: sensor.pinetwork_quorum_lag
            name: Verzögerung
            show:
              labels: true
            icon: 'mdi:timer'
            hours_to_show: 6
            points_per_hour: 10
            color_thresholds:
              - value: 50
                color: '#00FF00'
              - value: 300
                color: '#FFFF00'
              - value: 1000
                color: '#FF0000'
      - type: vertical-stack
        cards:
          - type: entities
            entities:
              - entity: switch.docker_pi_consensus
                name: Aktiv
              - type: 'custom:fold-entity-row'
                head: sensor.docker_pi_consensus_state
                open: true
                entities:
                  - entity: sensor.docker_pi_consensus_status
                    name: Laufzeit
                  - entity: sensor.docker_pi_consensus_memory
                    name: Arbeitsspeicher
                  - entity: sensor.docker_pi_consensus_cpu
                    name: CPU Auslastung
                  - entity: sensor.docker_pi_consensus_network_speed_down
                    name: Download (Aktuell)
                  - entity: sensor.docker_pi_consensus_network_total_down
                    name: Download (Gesamt)
                  - entity: sensor.docker_pi_consensus_network_speed_up
                    name: Upload (Aktuell)
                  - entity: sensor.docker_pi_consensus_network_total_up
                    name: Upload (Gesamt)
              - type: 'custom:mini-graph-card'
                entities:
                  - entity: sensor.docker_pi_consensus_cpu
                name: CPU Verbrauch
                show:
                  labels: true
                icon: 'mdi:cpu-64-bit'
                hours_to_show: 6
                points_per_hour: 10
                color_thresholds:
                  - value: 20
                    color: '#00FF00'
                  - value: 50
                    color: '#FFFF00'
                  - value: 80
                    color: '#FF0000'
              - type: 'custom:mini-graph-card'
                entities:
                  - entity: sensor.docker_pi_consensus_network_speed_up
                name: Upload
                show:
                  labels: true
                icon: 'mdi:upload'
                hours_to_show: 6
                points_per_hour: 10
                color_thresholds:
                  - value: 0
                    color: '#00FF00'
                  - value: 40
                    color: '#FFFF00'
                  - value: 100
                    color: '#FF0000'
              - type: 'custom:mini-graph-card'
                entities:
                  - entity: sensor.docker_pi_consensus_network_speed_down
                name: Download
                show:
                  labels: true
                icon: 'mdi:download'
                hours_to_show: 6
                points_per_hour: 10
                color_thresholds:
                  - value: 20
                    color: '#00FF00'
                  - value: 100
                    color: '#FFFF00'
                  - value: 500
                    color: '#FF0000'
            state_color: true
            title: Pi Network Blockchain Container


## Node-Red configuration
Import the following Nodes and Add your Mapquest and IP-API-Key into the Right "GetInfo"-Node. Configure your HomeAssistant-Instance in every Blue-Entity-Node. Also configure the Docker-Node to connect to your Docker-Instance
```json
[{"id":"a715c2da.5b8df8","type":"inject","z":"fb817191.49657","name":"GetInfo","props":[{"p":"payload"},{"p":"topic","vt":"str"}],"repeat":"120","crontab":"","once":false,"onceDelay":0.1,"topic":"","payload":"","payloadType":"date","x":120,"y":240,"wires":[["74e3c775.5f7458","a43a7abf.c3bab"]]},{"id":"75e892d8.ffee64","type":"json","z":"fb817191.49657","name":"","property":"payload","action":"","pretty":false,"x":170,"y":320,"wires":[["2a9dd397.ff0294"]]},{"id":"74e3c775.5f7458","type":"docker-container-actions","z":"fb817191.49657","name":"Pi Network Info","config":"6de6d99b.927c28","container":"pi-consensus","action":"exec","options":"curl 127.0.0.1:11626/info","x":140,"y":280,"wires":[["75e892d8.ffee64"]]},{"id":"1e787474.14c37c","type":"ha-entity","z":"fb817191.49657","name":"Status","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.state"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.state","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":390,"y":40,"wires":[[]]},{"id":"2a9dd397.ff0294","type":"function","z":"fb817191.49657","name":"format","func":"\n\nmsg.payload.info.startedOn = new Date(msg.payload.info.startedOn).toLocaleString(\"de-DE\").replace(\",\",\" um\")\nmsg.payload.info.build = msg.payload.info.build.substring(0, msg.payload.info.build.indexOf(\" (\"))\n\nreturn msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","x":170,"y":360,"wires":[["1e787474.14c37c","906655b1.43453","ac6a5105.ced1f","2267a2b.64b795e","a9ecb0e4.e23e68","124afbcd.b14754","e167fb0f.260e88","319ccfad.211738","684a0623.289228","b34bb26d.b5aca8","ca189569.16ef1","754dfcc6.fb3ff4","93e4e5f7.e3878","ee013a9.e3880c8","53dca540.7c582c"]]},{"id":"906655b1.43453","type":"ha-entity","z":"fb817191.49657","name":"Netzwerk","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.network"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.network","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":400,"y":100,"wires":[[]]},{"id":"ac6a5105.ced1f","type":"ha-entity","z":"fb817191.49657","name":"Fehlerrate","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.fehlerrate"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.history_failure_rate","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":400,"y":160,"wires":[[]]},{"id":"2267a2b.64b795e","type":"ha-entity","z":"fb817191.49657","name":"Version","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.version"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.build","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":400,"y":220,"wires":[[]]},{"id":"a9ecb0e4.e23e68","type":"ha-entity","z":"fb817191.49657","name":"Gestartet","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.startedOn"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.startedOn","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":400,"y":340,"wires":[[]]},{"id":"124afbcd.b14754","type":"ha-entity","z":"fb817191.49657","name":"Peers","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.peers"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.peers.authenticated_count","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":390,"y":400,"wires":[[]]},{"id":"e167fb0f.260e88","type":"ha-entity","z":"fb817191.49657","name":"Peers Pending","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.peersPending"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.peers.pending_count","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":420,"y":460,"wires":[[]]},{"id":"319ccfad.211738","type":"ha-entity","z":"fb817191.49657","name":"Protocol Version","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.protocolVersion"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.protocol_version","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":420,"y":280,"wires":[[]]},{"id":"684a0623.289228","type":"ha-entity","z":"fb817191.49657","name":"Quorum Nodes","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.quorum.nodes"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.quorum.transitive.node_count","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":420,"y":620,"wires":[[]]},{"id":"b34bb26d.b5aca8","type":"ha-entity","z":"fb817191.49657","name":"Quorum Übereinstimmungen","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.quorum.agreed"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.quorum.qset.agree","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":460,"y":680,"wires":[[]]},{"id":"ca189569.16ef1","type":"ha-entity","z":"fb817191.49657","name":"Quorum Ablehnungen","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.quorum.disagreed"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.quorum.qset.disagree","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":440,"y":740,"wires":[[]]},{"id":"754dfcc6.fb3ff4","type":"ha-entity","z":"fb817191.49657","name":"Quorum Fehler","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.quorum.failed"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.quorum.qset.fail_at","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":420,"y":800,"wires":[[]]},{"id":"93e4e5f7.e3878","type":"ha-entity","z":"fb817191.49657","name":"Quorum Lag","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.quorum.lag"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":"ms"}],"state":"payload.info.quorum.qset.lag_ms","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":410,"y":860,"wires":[[]]},{"id":"ee013a9.e3880c8","type":"ha-entity","z":"fb817191.49657","name":"Quorum Phase","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.quorum.phase"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.quorum.qset.phase","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":420,"y":920,"wires":[[]]},{"id":"53dca540.7c582c","type":"ha-entity","z":"fb817191.49657","name":"Quorum Node","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.quorum.node"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.info.quorum.node","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":420,"y":560,"wires":[[]]},{"id":"a43a7abf.c3bab","type":"docker-container-actions","z":"fb817191.49657","name":"Pi Network Info","config":"6de6d99b.927c28","container":"pi-consensus","action":"exec","options":"curl 127.0.0.1:11626/peers","x":120,"y":520,"wires":[["b4f61624.dd723"]]},{"id":"b4f61624.dd723","type":"json","z":"fb817191.49657","name":"","property":"payload","action":"","pretty":false,"x":90,"y":560,"wires":[["71ce7895.c626e8"]]},{"id":"71ce7895.c626e8","type":"function","z":"fb817191.49657","name":"format","func":"\nmsg.payload.authenticated_peers.outbound = msg.payload.authenticated_peers.outbound.length\nmsg.payload.authenticated_peers.inbound = msg.payload.authenticated_peers.inbound.length\nreturn msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","x":90,"y":600,"wires":[["f1279af8.3ca9c","aca91084.7dd6b"]]},{"id":"f1279af8.3ca9c","type":"ha-entity","z":"fb817191.49657","name":"Peers outgoing","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.peersOutgoing"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.authenticated_peers.outbound","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":420,"y":980,"wires":[[]]},{"id":"aca91084.7dd6b","type":"ha-entity","z":"fb817191.49657","name":"Peers ingoing","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.peersIngoing"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"payload.authenticated_peers.inbound","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":420,"y":1040,"wires":[[]]},{"id":"2b81d3a5.8b8e74","type":"split","z":"fb817191.49657","name":"","splt":"\\n","spltType":"str","arraySplt":1,"arraySpltType":"len","stream":false,"addname":"","x":800,"y":280,"wires":[["e256f2d6.994518"]]},{"id":"cb4bbe88.86ccc8","type":"http request","z":"fb817191.49657","name":"","method":"GET","ret":"txt","paytoqs":false,"url":"","persist":false,"authType":"","x":830,"y":360,"wires":[["defbda8c.d90ec"]]},{"id":"e256f2d6.994518","type":"function","z":"fb817191.49657","name":"prepare API Call","func":"msg.url = \"http://api.ipapi.com/\" + msg.payload + \"?access_key=\" + msg.ipapikey + \"&format=1\"\nreturn msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","x":820,"y":320,"wires":[["cb4bbe88.86ccc8"]]},{"id":"41967db4.d31954","type":"join","z":"fb817191.49657","name":"","mode":"custom","build":"array","property":"payload","propertyType":"msg","key":"topic","joiner":"\\n","joinerType":"str","accumulate":false,"timeout":"10","count":"","reduceRight":false,"reduceExp":"","reduceInit":"","reduceInitType":"","reduceFixup":"","x":770,"y":480,"wires":[["3bb37f06.7fef18"]]},{"id":"defbda8c.d90ec","type":"json","z":"fb817191.49657","name":"","property":"payload","action":"","pretty":false,"x":790,"y":400,"wires":[["4ddf32de.bfc0e4"]]},{"id":"4ddf32de.bfc0e4","type":"function","z":"fb817191.49657","name":"filter & format","func":"msg.payload = {\n    \"city\":msg.payload.city,\n    \"country\":msg.payload.country_name,\n    \"lat\":msg.payload.latitude,\n    \"lon\":msg.payload.longitude\n}\nreturn msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","x":820,"y":440,"wires":[["41967db4.d31954"]]},{"id":"3bb37f06.7fef18","type":"function","z":"fb817191.49657","name":"prepare Map-API call","func":"var locations = \"53.5465,8.0101|marker-red\"\nfor(var i in msg.payload){\n    var loc = msg.payload[i]\n    locations += \"||\" + loc.lat.toFixed(4) + \",\" + loc.lon.toFixed(4)\n}\nmsg.url = \"https://open.mapquestapi.com/staticmap/v5/map?format=png&size=1000,800&key=\" + msg.mapquestapikey + \"&locations=\" + locations\nmsg.updated = new Date().toLocaleString(\"de-DE\").replace(\",\",\" um\")\nreturn msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","x":820,"y":520,"wires":[["d54f83a2.74b7a8","c88474a3.fac04"]]},{"id":"c0170160.0f742","type":"file","z":"fb817191.49657","name":"","filename":"/Users/keno/docker/homeassistant/www/custom_images/connected_nodes.png","appendNewline":false,"createDir":true,"overwriteFile":"true","encoding":"none","x":1010,"y":600,"wires":[[]]},{"id":"d54f83a2.74b7a8","type":"http request","z":"fb817191.49657","name":"","method":"GET","ret":"bin","paytoqs":"ignore","url":"","tls":"","persist":false,"proxy":"","authType":"","x":810,"y":560,"wires":[["c0170160.0f742","45060736.ec128"]]},{"id":"c88474a3.fac04","type":"ha-entity","z":"fb817191.49657","name":"Image Updateted At","server":"d10b5b0f.f907d","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"pinetwork.peersImageUpdatedAt"},{"property":"device_class","value":""},{"property":"icon","value":""},{"property":"unit_of_measurement","value":""}],"state":"updated","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","outputOnStateChange":false,"outputPayload":"$entity().state ? \"on\": \"off\"","outputPayloadType":"jsonata","x":1100,"y":520,"wires":[[]]},{"id":"a14afd36.666e58","type":"inject","z":"fb817191.49657","name":"GetInfo","props":[{"p":"ipapikey","v":"","vt":"str"},{"p":"mapquestapikey","v":"","vt":"str"}],"repeat":"","crontab":"*/2 9-20 * * *","once":false,"onceDelay":0.1,"topic":"","x":800,"y":40,"wires":[["51a1aaaa.f73e04"]]},{"id":"796823ef.e47a2c","type":"json","z":"fb817191.49657","name":"","property":"payload","action":"","pretty":false,"x":790,"y":120,"wires":[["40b24c02.c8396c"]]},{"id":"51a1aaaa.f73e04","type":"docker-container-actions","z":"fb817191.49657","name":"Pi Network Info","config":"6de6d99b.927c28","container":"pi-consensus","action":"exec","options":"curl 127.0.0.1:11626/peers","x":820,"y":80,"wires":[["796823ef.e47a2c"]]},{"id":"40b24c02.c8396c","type":"function","z":"fb817191.49657","name":"filter","func":"\nmsg.payload = msg.payload.authenticated_peers.outbound\nreturn msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","x":790,"y":160,"wires":[["cb6936f3.3b6338"]]},{"id":"cb6936f3.3b6338","type":"join","z":"fb817191.49657","name":"","mode":"custom","build":"array","property":"payload","propertyType":"msg","key":"topic","joiner":"\\n","joinerType":"str","accumulate":false,"timeout":"3700","count":"30","reduceRight":false,"reduceExp":"","reduceInit":"","reduceInitType":"","reduceFixup":"","x":790,"y":200,"wires":[["1bdd005e.22ba8"]]},{"id":"1bdd005e.22ba8","type":"function","z":"fb817191.49657","name":"merge","func":"var unique = []\nvar data = msg.payload\n\nfor(var x in msg.payload){\n    for(var y in msg.payload[x]){\n        var value = msg.payload[x][y].address.substring(0,msg.payload[x][y].address.indexOf(\":\"))\n        if (!unique.includes(value)){\n            unique.push(value)\n        }\n    }\n}\nmsg.payload = unique\nreturn msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","x":790,"y":240,"wires":[["2b81d3a5.8b8e74"]]},{"id":"6de6d99b.927c28","type":"docker-configuration","host":"/var/run/docker.sock","port":""},{"id":"d10b5b0f.f907d","type":"server","name":"Home Assistant","legacy":false,"addon":false,"rejectUnauthorizedCerts":false,"ha_boolean":"y|yes|true|on|home|open","connectionDelay":true,"cacheJson":true}]
```
