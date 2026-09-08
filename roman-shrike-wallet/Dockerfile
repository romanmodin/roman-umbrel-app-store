FROM azul/zulu-openjdk:25-latest AS builder

RUN apt-get update && apt-get install -y git && rm -rf /var/lib/apt/lists/*

WORKDIR /src
RUN git clone --recursive https://github.com/privkeyio/shrike.git .
RUN ./gradlew jpackageImage
RUN cp "$JAVA_HOME/bin/java" /src/build/image/bin/java && chmod +x /src/build/image/bin/java

FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV DISPLAY=:99

RUN apt-get update && apt-get install -y \
    xvfb \
    x11vnc \
    novnc \
    websockify \
    fluxbox \
    libgtk-3-0 \
    libx11-6 \
    libx11-xcb1 \
    libxtst6 \
    libxi6 \
    libxrender1 \
    libxrandr2 \
    libasound2t64 \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /src/build/image /opt/shrike

RUN chmod +x /opt/shrike/bin/shrike /opt/shrike/bin/java

EXPOSE 6080

CMD bash -lc 'Xvfb :99 -screen 0 1280x800x24 >/tmp/xvfb.log 2>&1 & fluxbox >/tmp/fluxbox.log 2>&1 & x11vnc -display :99 -forever -shared -nopw -rfbport 5900 >/tmp/x11vnc.log 2>&1 & websockify --web=/usr/share/novnc/ 6080 localhost:5900 >/tmp/websockify.log 2>&1 & sleep 2 && /opt/shrike/bin/shrike'
