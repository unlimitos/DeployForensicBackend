root@speedtest:/home/huytq/ForensicBackend/ForensicBackend# docker ps
CONTAINER ID   IMAGE                                                 COMMAND                  CREATED          STATUS          PORTS                                                NAMES
6233814a3b14   forensicbackend-server                                "./forensic_services"    44 minutes ago   Up 44 minutes   10.27.0.2:9191->9191/tcp                             forensicbackend-server-1
06f0351c3750   docker.elastic.co/elasticsearch/elasticsearch:8.7.1   "/bin/tini -- /usr/l…"   44 minutes ago   Up 44 minutes   10.27.0.2:9200->9200/tcp, 10.27.0.2:9300->9300/tcp   elasticsearch
8d2f123b156d   docker.elastic.co/kibana/kibana:8.7.1                 "/bin/tini -- /usr/l…"   44 minutes ago   Up 4 minutes    10.27.0.2:5601->5601/tcp                             kibana


docker exec -u 0 -it 9ea8ead166f9 /bin/bash
nano kibana_user_role.json
{
    "cluster": ["monitor", "manage_index_templates", "manage_ingest_pipelines", "read_pipeline", "cluster:monitor/ingest/pipeline/stats","manage","manage_security","manage_api_key"],
    "indices": [
      {
        "names": [".kibana*", ".reporting-*", ".apm-agent-configuration", ".apm-custom-link"],
        "privileges": ["create_index","manage","all"],
        "allow_restricted_indices": true
      },
      {
        "names": ["*"],
        "privileges": ["view_index_metadata", "read","write"],
        "allow_restricted_indices": true
      }
    ],
    "applications": [
      {
        "application": "kibana-.kibana",
        "privileges": ["read_security","manage_security","all"],
        "resources": ["*"]
      }
    ],
    "run_as": [],
    "metadata": {},
    "transient_metadata": {
      "enabled": true
    }
  }


docker cp kibana_user_role.json d42fd225a8f5:/usr/share/elasticsearch/kibana_user_role.json

docker exec -u 0 -it d42fd225a8f5 /bin/bash
curl -X PUT "localhost:9200/_security/role/kibana_user_role" -H "Content-Type: application/json" -d @kibana_user_role.json -u elastic:EsNocmt2024

curl -X POST "localhost:9200/_security/user/nocmt" -H "Content-Type: application/json" -d '
{
  "password" : "EsNocmt2024",
  "roles" : [ "kibana_user_role","kibana_system","logstash_system","beats_system","apm_system","remote_monitoring_collector", "remote_monitoring_agent" ],
  "full_name" : "Nocmt User",
  "email" : "kibana_user@example.com"
}' -u elastic:EsNocmt2024

curl -X PUT "localhost:9200/_security/user/nocmt" -H "Content-Type: application/json" -d '
{
  "password" : "EsNocmt2024",
  "roles" : [ "kibana_user_role","kibana_system","logstash_system","beats_system","apm_system","remote_monitoring_collector", "remote_monitoring_agent" ],
  "full_name" : "Nocmt User",
  "email" : "kibana_user@example.com"
}' -u elastic:EsNocmt2024