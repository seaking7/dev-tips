
# kibana DEV TOOLS 실습
GET /_cat/health?v

PUT /customer?pretty

GET _cat/indices?v

POST customer/type1/1
{
  "name": "taekyung",
  "age": 30
}

GET customer/type1/1

DELETE customer/type1/1

DELETE /customer

POST customer/type1/1/_update
{
  "doc":{
    "age": 20  
  }
}

POST customer/type1/1/_update
{
  "script":{
    "inline": "if(ctx._source.age==20){ctx._source.age++}"
  }
}

# 퀴즈수행
PUT /tourcompany?pretty

POST tourcompany/customerlist/1
{
  "name":"Alfred",
  "phone":"010-1234-5678",
  "holiday_dest":"Disneyland",
  "departure_date":"2017/01/20"
}

POST tourcompany/customerlist/2
{
  "name":"huey",
  "phone":"010-2222-4444",
  "holiday_dest":"Disneyland",
  "departure_date":"2017/01/20"
}

POST tourcompany/customerlist/3
{
  "name":"Naomi",
  "phone":"010-3333-5555",
  "holiday_dest":"Hawaii",
  "departure_date":"2017/01/10"
}

POST tourcompany/customerlist/4
{
  "name":"Andra",
  "phone":"010-6666-7777",
  "holiday_dest":"Bora Bora",
  "departure_date":"2017/01/11"
}

POST tourcompany/customerlist/5
{
  "name":"Paul",
  "phone":"010-9999-8888",
  "holiday_dest":"Hawaii",
  "departure_date":"2017/01/10"
}

POST tourcompany/customerlist/6
{
  "name":"Colin",
  "phone":"010-5555-4444",
  "holiday_dest":"Venice",
  "departure_date":"2017/01/16"
}

DELETE tourcompany/customerlist/4

POST tourcompany/customerlist/3/_update
{
  "doc":{
    "departure_date":"2017/01/17"
  }
}

POST tourcompany/customerlist/5/_update
{
  "doc":{
    "departure_date":"2017/01/17"
  }
}

# bulk 배치 테스트
POST customer/type1/_bulk
{"index":{"_id":"1"}}
{"name":"taekyung"}
{"index":{"_id":"2"}}
{"name":"Gildong"}

GET customer/type1/2

POST customer/type1/_bulk
{"update":{"_id":"1"}}
{"doc":{"age":18}}
{"delete":{"_id":"2"}}

# 검색
GET /tourcompany/_search?q=*

POST /tourcompany/_search
{
  "query":{"match_all":{}},
  "from":2,
  "size":2
}

POST /tourcompany/_search
{  
    "query":{"match":{"holiday_dest":"Hawaii"}}
}

# 샘플데이터 insert후 검색
GET /_cat/indices

GET kibana_sample_data_flights/_search?q=*

GET kibana_sample_data_flights/_search?q=*&sort=AvgTicketPrice

POST kibana_sample_data_flights/_search
{
  "query":{"match_all": {}},
  "sort": {"AvgTicketPrice":"asc"}
}

POST kibana_sample_data_flights/_search
{
  "query":{"match_all": {}},
  "sort": {"AvgTicketPrice":"desc"},
  "_source": ["AvgTicketPrice", "FlightNum"]
}

POST kibana_sample_data_flights/_search
{
  "query":{"match": {
    "DestCountry": "AU"
  }},
  "sort": {"AvgTicketPrice":"desc"},
  "_source": ["AvgTicketPrice", "FlightNum", "DestCountry"]
}

POST kibana_sample_data_flights/_search
{
  "query":{
    "bool":{
      "must":{"match":{"DestCountry":"AU"}},
      "must_not":{"match":{"FlightNum":"67MHXJA"}}
    }
  }
}

# URL 로 검색
http://13.124.37.156:9200/kibana_sample_data_flights/_search?q=*&explain
http://13.124.37.156:9200/kibana_sample_data_flights/_search?q=OriginWeather:Sunny&explain
http://13.124.37.156:9200/kibana_sample_data_flights/_search?q=OriginWeather:Sunny&_source=DestCountry,OriginWeather,OriginCityName

GET kibana_sample_data_flights/_doc/_search?q=Sunny
GET kibana_sample_data_flights/_doc/_search?q=OriginWeather:Sunny AND DestCountry:AU
GET kibana_sample_data_flights/_doc/_search?q=OriginWeather:Sunny AND DestCountry:AU&_source=OriginWeather,DestCountry,FlightNum
GET kibana_sample_data_flights/_doc/_search?q=OriginWeather:Sunny AND DestCountry:AU&_source=OriginWeather,DestCountry,FlightNum,AvgTicketPrice&sort=AvgTicketPrice

# 퀴즈2
GET tourcompany/customerlist/_search?q="010-3333-5555"
GET tourcompany/customerlist/_search?q=holiday_dest:"Disneyland"&_source=phone
GET tourcompany/customerlist/_search?q=departure_date:"2017/01/17" OR "2017/01/20"&_source=name,departure_date&sort=name.keyword
POST tourcompany/customerlist/_delete_by_query?q="Colin"

# 그룹함수
GET tourcompany/customerlist/_search
{
  "size":0,
  "aggs":{
    "group_by_state":{
      "terms":{"field":"holiday_dest.keyword"}
    }
  }
}

GET kibana_sample_data_flights/_search

GET kibana_sample_data_flights/_search
{
  "size":0,
  "aggs":{
    "group_by_state":{
      "terms":{"field":"DestCountry"},
      "aggs":{
        "average_balance":{
          "avg":{"field": "AvgTicketPrice"}
        }
      }
    }
  }
}


# 무료강의실습
curl -XPUT http://localhost:9200/classes
curl -XGET http://localhost:9200/classes?pretty
curl -XPOST http://localhost:9200/classes/class/1 -H'Content-Type: application/json' -d '
{"title":"Algorithm", "professor":"John"}'
curl -XPOST http://localhost:9200/classes/class/1 -H'Content-Type: application/json'  -d @oneclass.json
curl -XGET http://localhost:9200/classes/class/1?pretty

# bulk insert
curl -XPOST http://localhost:9200/_bulk?pretty  -H'Content-Type: application/json' --data-binary @classes.json
curl -XDELETE http://localhost:9200/classes
curl -XPUT http://localhost:9200/classes
curl -XPUT http://localhost:9200/classes/class/_mapping  -H'Content-Type: application/json' -d @classesRating_mapping.json

# ch03 search
curl -XPOST http://localhost:9200/_bulk?pretty  -H'Content-Type: application/json' --data-binary @simple_basketball.json
curl -XGET 'http://localhost:9200/basketball/record/_search?q=points:30&pretty'
curl -XGET localhost:9200/_search?pretty -H'Content-Type: application/json' --data-binary @avg_points_aggs.json
curl -XGET localhost:9200/_search?pretty -H'Content-Type: application/json' --data-binary @max_points_aggs.json
curl -XGET localhost:9200/_search?pretty -H'Content-Type: application/json' --data-binary @min_points_aggs.json



# 7.2버전 샘플데이터 load
https://www.elastic.co/guide/en/kibana/7.2/tutorial-build-dashboard.html

PUT /shakespeare
{
  "mappings": {
    "properties": {
    "speaker": {"type": "keyword"},
    "play_name": {"type": "keyword"},
    "line_id": {"type": "integer"},
    "speech_number": {"type": "integer"}
    }
  }
}

PUT /logstash-2015.05.18
{
  "mappings": {
    "properties": {
      "geo": {
        "properties": {
          "coordinates": {
            "type": "geo_point"
          }
        }
      }
    }
  }
}


PUT /logstash-2015.05.19
{
  "mappings": {
    "properties": {
      "geo": {
        "properties": {
          "coordinates": {
            "type": "geo_point"
          }
        }
      }
    }
  }
}

PUT /logstash-2015.05.20
{
  "mappings": {
    "properties": {
      "geo": {
        "properties": {
          "coordinates": {
            "type": "geo_point"
          }
        }
      }
    }
  }
}

curl -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/bank/_bulk?pretty' --data-binary @accounts.json
curl -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/shakespeare/_bulk?pretty' --data-binary @shakespeare.json
curl -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/_bulk?pretty' --data-binary @logs.jsonl