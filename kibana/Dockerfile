#escape=`
FROM microsoft/nanoserver as build-stage

# Build variable to allow passing in a specific version of Elasticsearch to download
ARG KIBANA_VERSION=5.6.2
ARG NODE_VERSION=8.9.4

# Download and extract Kibana
ADD ["https://artifacts.elastic.co/downloads/kibana/kibana-${KIBANA_VERSION}-windows-x86.zip", "/"]

# Extracting the archive can take a while unless this step is cached already
RUN powershell.exe -Command `
  $fullversion = 'kibana-'+$env:KIBANA_VERSION+'-windows-x86'; `
  $zipPath = $fullversion+'.zip'; `
  Expand-Archive -Path $zipPath -DestinationPath ./ -Force; `
  Rename-Item -Path $fullversion -NewName kibana;

  
ADD ["https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-win-x64.zip", "/"]

RUN powershell.exe -Command `
  $fullversion = 'node-v'+$env:NODE_VERSION+'-win-x64'; `
  $zipPath = $fullversion+'.zip'; `
  Expand-Archive -Path $zipPath -DestinationPath ./ -Force; `
  Copy-Item -Path ($fullversion+'\**\') -Destination kibana -Force -Recurse; 

COPY kibana.yml \kibana\config\
FROM microsoft/windowsservercore

COPY --from=build-stage c:\kibana c:\kibana
RUN c:\kibana\bin\kibana-plugin.bat install x-pack

CMD c:\kibana\node\node.exe --no-warnings c:\kibana\src\cli

EXPOSE 5601
