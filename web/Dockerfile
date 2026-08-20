ARG JITSI_REPO=ghcr.io/jitsi
ARG BASE_TAG=latest
FROM ${JITSI_REPO}/base:${BASE_TAG}

USER root

LABEL org.opencontainers.image.title="Jitsi Meet"
LABEL org.opencontainers.image.description="WebRTC compatible JavaScript application that uses Jitsi Videobridge to provide high quality, scalable video conferences."
LABEL org.opencontainers.image.url="https://jitsi.org/jitsi-meet/"
LABEL org.opencontainers.image.source="https://github.com/jitsi/docker-jitsi-meet"
LABEL org.opencontainers.image.documentation="https://jitsi.github.io/handbook/"

ADD https://raw.githubusercontent.com/acmesh-official/acme.sh/3.0.7/acme.sh /opt
COPY rootfs/ /

RUN \
  apt-dpkg-wrap apt-get update && \
  apt-dpkg-wrap apt-get install -y \
    dnsutils \
    jitsi-meet-web \
    jq \
    nginx-extras \
    socat \
    && \
  mv /usr/share/jitsi-meet/interface_config.js /defaults && \
  chmod 0755 /opt/acme.sh && \
  ln -s /run/web/.well-known /usr/share/jitsi-meet/.well-known && \
  rm -f /etc/nginx/conf.d/default.conf && \
  rm -rf /var/lib/nginx && \
  ln -s /run/web/tmp /var/lib/nginx && \
  mkdir -p /storage && \
  chown s6:s6 /storage && \
  apt-cleanup

# Copy custom branding files into the container
COPY branding/ /branding/

# Apply branding customizations
RUN \
  cp /branding/favicon.ico /usr/share/jitsi-meet/favicon.ico && \
  cp /branding/favicon.png /usr/share/jitsi-meet/images/favicon.png && \
  cp /branding/apple-touch-icon.png /usr/share/jitsi-meet/images/apple-touch-icon.png && \
  cp /branding/logo-white.png /usr/share/jitsi-meet/images/watermark.png && \
  cp /branding/logo-white.png /usr/share/jitsi-meet/images/logo-deep-linking.png && \
  echo '<title>Think.4U Meet</title>' > /usr/share/jitsi-meet/title.html && \
  cat /branding/custom-styles.css >> /usr/share/jitsi-meet/css/all.css && \
  cat /branding/custom-interface_config.js >> /defaults/interface_config.js && \
  jq '.welcomepage.title = "Think.4U Video Meetings" | .welcomepage.headerTitle = "Think.4U Video Meetings" | .welcomepage.subTitle = "Secure, high-quality video conferencing for Think.4U." | .appDescription = "Join secure virtual rooms linked to your Think.4U charity portal."' /usr/share/jitsi-meet/lang/main.json > /tmp/main.json && mv /tmp/main.json /usr/share/jitsi-meet/lang/main.json

EXPOSE 8000 8443

VOLUME ["/config", "/usr/share/jitsi-meet/transcripts"]

USER s6
