# Livepeer PromGraf

This docker compose file will start up a prometheus and grafana connected to a livepeer node running on the host machine. 

## Setup 

Since prometheus is running from within a docker container we'll have to point it to the livepeer-node services running on the host machine. 

The livepeer-node exposes monitoring on port 7935 by default. This is the same port as the livepeer-cli is running on.

### Running promgraf

```
git clone https://github.com/kyriediculous/livepeer-promgraf.git && cd livepeer-promgraf

docker-compose up 
```

### B and O on the same machine
If you are running both a broadcaster with `-cliAddr :7936` and an orchestrator+transcoder with `-cliAddr :7935` you should alter the config in `prometheus.yml` to match that.

```
  - job_name: livepeer-node
    scrape_interval: 5s
    static_configs:
      - targets:
          - host.docker.internal:7935
        labels:
          livepeer_node_type: transcoder
      - targets:
          - host.docker.internal:7935
        labels:
          livepeer_node_type: orchestrator
      - targets:
          - host.docker.internal:7936
        labels:
          livepeer_node_type: broadcaster
```

### Orchestrator-transcoder split 
if you are running a single orchestrator with multiple remote transcoders, the setup will look something like

```
  - job_name: livepeer-node
    scrape_interval: 5s
    static_configs:
      - targets:
          - host.docker.internal:7935
        labels:
          livepeer_node_type: orchestrator
      - targets:
          - <transcoder_1 IP ADDRESS>:7935
        labels:
          livepeer_node_type: transcoder_1
      - targets:
          - <trancsoder_2 IP ADDRESS>:7935
        labels:
          livepeer_node_type: transcoder_2
```