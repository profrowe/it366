#### Cluster is Yellow

Check the shards:
If `curl -x GET /_cat/shards?v` returns unassigned, it's because it's only a single node.  This rather dirty hack will blow away all your data but reset the template to only permit 1 shard and 0 replicas.  I fixed the configs @ 5pm on 2/11/19 to not do this, but if you ran the configs before, you will need to do this:

Run for each beat:

```bash
curl -X DELETE "localhost:9200/_template/auditbeat-6.6.0"
sudo auditbeat setup --template -E output.logstash.enabled=false -E output.elasticsearch.enabled=true -E 'output.elasticsearch.hosts=["localhost:9200"]' -E output.elasticsearch.template.overwrite=true -E setup.template.settings.index.number_of_shards=1
curl -X DELETE "localhost:9200/auditbeat-*"
```

