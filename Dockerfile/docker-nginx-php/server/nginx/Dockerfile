FROM nginx:1.12

MAINTAINER Wongzx <Wongzx@wong.com>

# set timezome
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
