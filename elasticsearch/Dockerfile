#escape=`
FROM microsoft/nanoserver as build-image

# Build variable to allow passing in a specific version of Elasticsearch to download
ARG ELASTICSEARCH_VERSION=5.6.2

# Download and extract Elasticsearch
SHELL ["powershell.exe", "-Command"]

ADD ["https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-${ELASTICSEARCH_VERSION}.zip", "/"]
RUN Expand-Archive -Path \elasticsearch-$($Env:ELASTICSEARCH_VERSION).zip -DestinationPath \ ; `
    Remove-Item -Path \elasticsearch-$($Env:ELASTICSEARCH_VERSION).zip ;`
	Rename-Item -Path \elasticsearch-$($Env:ELASTICSEARCH_VERSION) -NewName elasticsearch;

COPY elasticsearch.yml \elasticsearch\config\
COPY jvm.options \elasticsearch\config\
COPY log4j2.properties \elasticsearch\config\

#RUN \elasticsearch\bin\elasticsearch-plugin.bat install x-pack
	
FROM openjdk:8-nanoserver

COPY --from=build-image c:\elasticsearch c:\elasticsearch

# Create a data volume and map it to the G: drive, allowing Java to call Path.toRealPath() successfully
VOLUME C:\data\
RUN Set-Variable -Name 'regpath' -Value 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\DOS Devices' ; `
    Set-ItemProperty -path $regpath -Name 'G:' -Value '\??\C:\data\' -Type String ;

RUN C:\elasticsearch\bin\elasticsearch-plugin.bat install x-pack --batch
CMD C:\elasticsearch\bin\elasticsearch.bat

EXPOSE 9200 9300
