FROM openfaas/classic-watchdog:0.18.0 as watchdog

FROM arm64v8/node:8.9.1

COPY --from=watchdog /fwatchdog /usr/bin/fwatchdog
RUN chmod +x /usr/bin/fwatchdog

WORKDIR /root/

# Turn down the verbosity to default level.
ENV NPM_CONFIG_LOGLEVEL warn

# Wrapper/boot-strapper
COPY package.json       .
RUN npm i

# Function
COPY index.js           .
RUN mkdir -p ./root/function

COPY function/*.json    ./function/
WORKDIR /root/function
RUN npm i || :
WORKDIR /root/
COPY function           function
WORKDIR /root/function

WORKDIR /root/

ENV cgi_headers="true"

ENV fprocess="node index.js"
EXPOSE 8080

HEALTHCHECK --interval=3s CMD [ -e /tmp/.lock ] || exit 1

CMD ["fwatchdog"]
