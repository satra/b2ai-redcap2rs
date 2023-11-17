### Converting bridge2ai redcap to reproschema

```shell
mkdir b2ai-schema
cd b2ai-schema
```

Download the csv from the redcap repo (https://github.com/eipm/bridge2ai-redcap/tree/main/v2.0.0) into this folder.

This process builds a container to run the redcap2reproschema conversion. It uses an old node and an old repo to do this into an older version of reproschema. We intend to rework the converter into a python script after our next update of reproschema.

```shell
docker run --rm repronim/neurodocker:master generate docker --base-image=node:13.12.0-buster --pkg-manager=apt --miniconda env_name=nidm env_exists=false conda_install='python=3 pandas pip datalad'  pip_install=pynidm version=latest --run-bash "cd /opt && curl -O -sSL https://github.com/ReproNim/reproschema-builder/archive/c4398f57c271b9705028f0775a35071ec5e210e6.tar.gz && tar zxf c439* && rm *.tar.gz && mv repro* r2s && cd r2s && npm install -g" --workdir /data | docker build -t rs:latest --platform linux/amd64 -
```

Now run the built container with the redcap csv data dictionary file in your current directory.

```shell
docker run -it --rm --platform linux/amd64 -v $(pwd):/data --entrypoint bash rs:latest
```

inside the container execute this command to convert the redcap data dictionary.

```shell
node /opt/r2s/tools/RedCap2ReproSchema.js 20231114201054_DataDictionary_cf3de2.csv
```

after running the above you should have the protocols and activities as json structures (technically jsonld), but you can process them as json
