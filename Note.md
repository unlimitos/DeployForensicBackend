docker exec -it 23d8984e631e /bin/bash
# replace string
sed -i 's/8080/9191/g' .env

#kibana script for elastic search
GET ${listIndices}
GET ${listAlias}
DELETE currentuser
GET currentuser/_search

# Search value and return selet field
GET ipconfiguration/_search
{
  "query": {
    "match": {
      "values.Description": "Intel(R) Ethernet Connection (14) I219-LM"
    }
  },
  "_source": [false], 
  "fields": [
    "values.Description"
  ]
}

GET ipconfiguration/_search
{
  "query": {
    "match_all": {}
  },
  "_source": [false], 
  "fields": [
    "values.IpAddress", 
    "values.Description"
  ]
}