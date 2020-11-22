1、安装配置

```shell
https://artifacts.elastic.co/downloads/logstash/logstash-5.5.1.tar.gz
Hello world
./logstash -e 'input{stdin{}}output{stdout{codec=>rubydebug}}'
(base) [root@zeppelin bin]# ./logstash -e 'input{stdin{}}output{stdout{codec=>rubydebug}}'
ERROR StatusLogger No log4j2 configuration file found. Using default configuration: logging only errors to the console.
Sending Logstash's logs to /app/logstash-5.5.1/logs which is now configured via log4j2.properties
[2019-07-20T14:22:06,445][INFO ][logstash.agent           ] No persistent UUID file found. Generating new UUID {:uuid=>"e49c93e3-f4cd-4276-8a62-af7d20f543df", :pa
th=>"/app/logstash-5.5.1/data/uuid"}[2019-07-20T14:22:06,771][INFO ][logstash.pipeline        ] Starting pipeline {"id"=>"main", "pipeline.workers"=>4, "pipeline.batch.size"=>125, "pipeline.batch.de
lay"=>5, "pipeline.max_inflight"=>500}[2019-07-20T14:22:06,825][INFO ][logstash.pipeline        ] Pipeline main started
The stdin plugin is now waiting for input:
[2019-07-20T14:22:06,940][INFO ][logstash.agent           ] Successfully started Logstash API endpoint {:port=>9600}
Hello world
{
    "@timestamp" => 2019-07-20T18:23:08.759Z,
      "@version" => "1",
          "host" => "zeppelin",
       "message" => "Hello world"
}

vi logstash.conf
input {
    stdin { }
}
output {
    stdout {
        codec => rubydebug {}
    }
    elasticsearch {
        Rost=>["127.0.0.1"]
}
}


```

2.

```
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
cat > /etc/yum.repos.d/elk.repo <<EOF
> [logstash-5.x]
> name=Elastic repository for 5.x packages
> baseurl=https://artifacts.elastic.co/packages/5.x/yum
> gpgcheck=1
> gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
> enabled=1
> autorefresh=1
> type=rpm-md
> EOF

yum install -y logstash
/usr/share/logstash

 bin/logstash-plugin list

```

