FROM elasticsearch:5.5.0

# set timezome
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# install ik plugins
COPY ./exts/elasticsearch-analysis-ik-5.5.0.zip /tmp/
RUN cd /usr/share/elasticsearch/plugins \
    && mkdir ik && cd ik \
    && unzip /tmp/elasticsearch-analysis-ik-5.5.0.zip

# clean tmp files
RUN rm /tmp/elasticsearch-analysis-ik-5.5.0.zip