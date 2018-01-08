FROM microsoft/nanoserver

# Build variable to allow passing in a specific version of Elasticsearch to download
ARG KIBANA_VERSION=5.4.0
ARG NODE_VERSION=8.9.4

# Download and extract 64-bit Node.js 
ADD ["https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-win-x64.zip", "/"]

RUN powershell.exe -Command \
  $fullversion = 'node-v'+$env:NODE_VERSION+'-win-x64'; \
  $zipPath = $fullversion+'.zip'; \
  Expand-Archive -Path $zipPath -DestinationPath ./ -Force; \
  Remove-Item -Path $zipPath; \
  Rename-Item -Path $fullversion -NewName kibana; 

# Download and extract Kibana
ADD ["https://artifacts.elastic.co/downloads/kibana/kibana-${KIBANA_VERSION}-windows-x86.zip", "/"]

# Extracting the archive can take a while unless this step is cached already
RUN powershell.exe -Command \
  $fullversion = 'kibana-'+$env:KIBANA_VERSION+'-windows-x86'; \
  $zipPath = $fullversion+'.zip'; \
  Expand-Archive -Path $zipPath -DestinationPath ./ -Force; \
  Remove-Item -Path $zipPath; \
  $pathPattern = $fullversion+'/**/'; \
  Copy-Item -Path $pathPattern -Destination kibana -Force -Recurse;\
  Remove-Item -Path $fullversion -Recurse -Force;

# ELASTIC_HOME is used by the runelasticsearch.cmd file to launch Elasticsearch.
ENV ELASTIC_HOME C:\\elasticsearch-${KIBANA_VERSION}

COPY kibana.yml /kibana/config/
COPY kibana.bat /kibana/bin/

WORKDIR C:/kibana
CMD [".\\bin\\kibana.bat"]

EXPOSE 5601
