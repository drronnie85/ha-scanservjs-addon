FROM sbs20/scanservjs:v3.0.3
USER root

# (опційно) bashio — якщо реально використовуєш у run.sh/config
RUN export DEBIAN_FRONTEND=noninteractive \
 && apt-get update \
 && apt-get install -y --no-install-recommends jq ca-certificates sane-utils curl libusb-0.1-4 \
 && curl -fsSL -o /tmp/brscan3.deb \
      "https://download.brother.com/welcome/dlf006642/brscan3-0.2.13-1.amd64.deb" \
 && dpkg -i /tmp/brscan3.deb || apt-get -y -f install \
 && echo "brother3" >> /etc/sane.d/dll.conf \
 && printf '%s\n' 'usb 0x04f9 0x01ea' >> /etc/sane.d/brother3.conf \
 # інколи бібліотеки кладуться в /usr/lib64/sane — робимо симлінк
 && ln -sf /usr/lib64/sane/libsane-brother* /usr/lib/x86_64-linux-gnu/sane/ || true \
 && ln -sf /usr/lib64/sane/libsane-brother* /usr/lib/sane/ || true \
 && ldconfig || true \
 # udev-правило, щоб USB-сканер не впирався у права
 && printf '%s\n' \
 'ATTRS{idVendor}=="04f9", MODE="0660", GROUP="scanner", ENV{libsane_matched}="yes"' \
    > /etc/udev/rules.d/60-brother-scanner.rules \
 && apt-get purge -y curl \
 && rm -rf /var/lib/apt/lists/* /tmp/brscan3.deb

# твій конфіг scanservjs (якщо треба)
COPY config.local.js /etc/scanservjs/config.local.js

# стартовий скрипт
COPY run.sh /run.sh
RUN chmod +x /run.sh

EXPOSE 8080
CMD ["/run.sh"]