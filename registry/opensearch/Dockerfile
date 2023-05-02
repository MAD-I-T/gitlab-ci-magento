FROM opensearchproject/opensearch:2.5.0

USER root
RUN yum -y install zip && \
    zip -q -d /usr/share/opensearch/lib/log4j-core-*.jar org/apache/logging/log4j/core/lookup/JndiLookup.class && \
    yum remove -y zip && \
    yum -y clean all && \
    rm -rf /var/cache
USER opensearch

RUN bin/opensearch-plugin install -b analysis-icu && \
    bin/opensearch-plugin install -b analysis-phonetic


EXPOSE 9200 9300