FROM swipl:7.6.4

RUN apt-get update && apt-get install -y build-essential

ENV BASEDIR ${HOME}/candc
COPY candc ${BASEDIR}

# install SOAP
WORKDIR ${BASEDIR}/ext
RUN apt-get install -y unzip
RUN unzip gsoap_2.8.16.zip
WORKDIR gsoap-2.8
RUN apt-get install -y bison flex
RUN apt-get install -y libcrypto++-dev libssl1.0-dev zlib1g-dev
RUN ./configure --prefix=$BASEDIR/ext && make && make install

# compile the C&C tools with SOAP support
WORKDIR ${BASEDIR}
RUN cp Makefile.unix Makefile
RUN make && make soap

# compile tokenizer and Boxer
RUN make bin/t && make bin/boxer

# unzip the models for the parser
RUN tar -xjvf models-1.02.tbz2

RUN rm -rf ext/gsoap* models*.tbz2

FROM swipl:7.6.4
COPY --from=0 ${HOME}/candc candc
WORKDIR candc
CMD bin/candc --models models/boxer --candc-printer boxer | tee /dev/stderr | bin/boxer --stdin --box true
