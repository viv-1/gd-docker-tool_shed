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

Example with samtools sort

Add tool on Tool Shed :

samtools_sort_docker repository : repository type : Unrestricted
samtools_sort.xml

```
<tool id="samtools_sort" name="Sort" version="0.1">
    <description>BAM dataset</description>
    <requirements>
        <requirement type="package" version="0.1">docker_samtools</requirement>
        <container type="docker">viv1/samtools:1.2</container>
    </requirements>
    <command>samtools sort $sort_mode -@ \${GALAXY_SLOTS:-1} -o "${output1}" -O bam -T dataset "${input1}"</command>
    <inputs>
        <param name="input1" type="data" format="bam" label="BAM File" />
        <param name="sort_mode" type="select" label="Sort by ">
            <option value="" selected="True">Chromosomal coordinates</option>
            <option value="-n">Read names (-n)</option>
        </param>
    </inputs>
    <outputs>
        <data name="output1" format="bam" />
    </outputs>
    <help>

**What it does**

This tool uses ``samtools sort`` command to sort BAM datasets in coordinate or read name order.
    </help>
</tool>
```

tool_dependencies.xml 

```
<?xml version="1.0"?>
<tool_dependency>
    <package name="docker_samtools" version="0.1">
        <repository changeset_revision="a25090e7392f" name="docker_samtools" owner="jbrayet" toolshed="https://testtoolshed.g2.bx.psu.edu" />
    </package>
</tool_dependency>
```

docker_samtools repository : repository type : Tool dependency definition

tool_dependencies.xml

```
<?xml version="1.0"?>
<tool_dependency>
    <package name="docker_samtools" version="0.1">
        <install version="1.0">
            <actions>
                <action type="shell_command">docker pull viv1/samtools:1.2</action>
            </actions>
        </install>
    </package>
</tool_dependency>
```




