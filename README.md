```shell
$ docker-compose build

$ docker-compose up -d

$ docker container ls
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                              NAMES
27e162f55d4a        es-multi-node_es01   "/usr/local/bin/dock…"   2 minutes ago       Up 2 minutes        9300/tcp, 0.0.0.0:9300->9200/tcp   es01
7d0dcd33e202        es-multi-node_es02   "/usr/local/bin/dock…"   27 minutes ago      Up 27 minutes       9200/tcp, 9300/tcp                 es02

$ docker-compose ps
Name              Command               State                Ports
--------------------------------------------------------------------------------
es01   /usr/local/bin/docker-entr ...   Up      0.0.0.0:9300->9200/tcp, 9300/tcp
es02   /usr/local/bin/docker-entr ...   Up      9200/tcp, 9300/tcp
$
$ curl -X GET "localhost:9300/_cluster/health?pretty"
{
  "cluster_name" : "docker-cluster",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 2,
  "number_of_data_nodes" : 2,
  "active_primary_shards" : 0,
  "active_shards" : 0,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}

$ curl -X GET "localhost:9300/_cat/health?v"
epoch      timestamp cluster        status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1605829558 23:45:58  docker-cluster green           2         2      0   0    0    0        0             0                  -                100.0%
$

$ curl -X PUT "localhost:9300/customer?pretty"

$ curl -X GET "localhost:9300/_cat/indices?v&pretty"
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   customer -P9iu6KjRA29rtqD1sqYRQ   1   1          0            0       460b           230b

# shard,replica 数を指定して作る場合
$ curl -X PUT 'localhost:9300/bank' -H 'Content-Type: application/json' -d'
{
    "settings" : {
        "index" : {
            "number_of_shards" : 5,
            "number_of_replicas" : 1
        }
    }
}
'

$ watch -n 1 'docker container exec -it es01 bash -c "ls data/nodes/0/indices/"'
$ watch -n 1 'docker container exec -it es02 bash -c "ls data/nodes/0/indices/"'


$ curl -X PUT "localhost:9300/test?pretty"
$ curl -X DELETE "localhost:9300/test?pretty"
```