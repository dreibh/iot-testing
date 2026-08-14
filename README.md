# IoT DB via Docker

Documentation: [https://hub.docker.com/r/apache/iotdb](https://hub.docker.com/r/apache/iotdb)

## Setup `iotdb-setup`

```bash
docker network create --driver=bridge --subnet=169.254.100.0/24 --gateway=169.254.100.1 dbtest
docker run -d --name iotdb-service \
              --hostname iotdb-service \
              --network dbtest \
              --ip 169.254.100.100 \
              -p 6667:6667 \
              -e cn_internal_address=iotdb-service \
              -e cn_seed_config_node=iotdb-service:10710 \
              -e cn_internal_port=10710 \
              -e cn_consensus_port=10720 \
              -e dn_rpc_address=iotdb-service \
              -e dn_internal_address=iotdb-service \
              -e dn_seed_config_node=iotdb-service:10710 \
              -e dn_mpp_data_exchange_port=10740 \
              -e dn_schema_region_consensus_port=10750 \
              -e dn_data_region_consensus_port=10760 \
              -e dn_rpc_port=6667 \
              apache/iotdb:2.0.10-standalone
```

It takes a few seconds to initialise the database (Java) ...

```bash
docker exec -ti iotdb-service /iotdb/sbin/start-cli.sh -h iotdb-service
```

## Removal `iotdb-destroy`

```bash
docker rm -f iotdb-service
docker network remove -f dbtest
```


# IoT Benchmark

```bash
docker run -it --rm --network dbtest --hostname benchmark --volume "$(pwd)":/iot-testing ubuntu:24.04
```

```bash
cd /iot-testing
./prepare-iot-benchmark

cd ~/src/iot-benchmark/iotdb-2.0/target/iot-benchmark-iotdb-2.0/iot-benchmark-iotdb-2.0
```

In `conf/config.properties`:
```
HOST=169.254.100.100
OPERATION_PROPORTION=1:1:1:1:1:1:1:1:1:1:1:1:1
```
