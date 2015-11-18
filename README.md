# gd-docker-tool_shed

# galaxy-dist/config modifications :

galaxy.ini :

Replace : 

```
# tool_dependency_dir = None
```

by

```
tool_dependency_dir = ../tool_dependencies
```

Copy job_conf file :

```
cp job_conf.xml.sample_basic job_conf.xml
```

job_conf.xml :

Replace : 

```
<?xml version="1.0"?>
<!-- A sample job config that explicitly configures job running the way it is configured by default (if there is no explicit  config). -->
    <job_conf>
        <plugins>
            <plugin id="local" type="runner" load="galaxy.jobs.runners.local:LocalJobRunner" workers="4"/>
    </plugins>
    <handlers>
        <handler id="main"/>
    </handlers>
    <destinations>
        <destination id="local" runner="local"/>
    </destinations>
</job_conf>
```

by

```
<?xml version="1.0"?>
<!-- A sample job config that explicitly configures job running the way it is configured by default (if there is no explicit config). -->
    <job_conf>
        <plugins>
            <plugin id="local" type="runner" load="galaxy.jobs.runners.local:LocalJobRunner" workers="4"/>
    </plugins>
    <handlers>
        <handler id="main"/>
    </handlers>
    <destinations default="docker_local">
       <destination id="local" runner="local"/>
       <destination id="docker_local" runner="local">
         <param id="docker_enabled">true</param>
       </destination>
    </destinations>
</job_conf>
```

# Docker

1. Create the Dockerfile with require tools and wrapper script if require

2. Add the docker image to docker hub

The first solution is to build locally an push to docker repository
 ```
docker build -t viv1/samtools:1.2 -f Dockerfile ./
docker push viv1/samtools:1.2
 ```

The second solution is to link a github to the docker hub account and use autobuild functionnality

# Tool xml wrapper

add 

```
<requirements>
  <container type="docker">samtools-1.2</container>
</requirements>
```

# Tool Shed 





