
# build on an ARM machine (cubox-i)
# docker build -t docker-cupsd .

FROM debian:bullseye
MAINTAINER hardcoredaniel@seznam.cz

# Install Packages (basic tools, cups, basic drivers, HP drivers)
RUN apt-get update \
&& apt-get install -y \
  sudo \
  whois \
  usbutils \
  cups \
  cups-client \
  cups-bsd \
  cups-filters \
  escputil \
  foomatic-db-compressed-ppds \
  printer-driver-all \
  openprinting-ppds \
  hpijs-ppds \
  hp-ppd \
  hplip \
  smbclient \
  printer-driver-cups-pdf \
&& apt-get clean \
&& rm -rf /var/lib/apt/lists/*

# Add user and disable sudo password checking
RUN useradd \
  --groups=sudo,lp,lpadmin \
  --create-home \
  --home-dir=/home/print \
  --shell=/bin/bash \
  --password=$(mkpasswd print) \
  print \
&& sed -i '/%sudo[[:space:]]/ s/ALL[[:space:]]*$/NOPASSWD:ALL/' /etc/sudoers

# Configure the service's to be reachable
RUN /usr/sbin/cupsd \
  && while [ ! -f /var/run/cups/cupsd.pid ]; do sleep 1; done \
  && cupsctl --remote-admin --remote-any --share-printers \
  && kill $(cat /var/run/cups/cupsd.pid)

# Patch the default configuration file to only enable encryption if requested
RUN sed -e '0,/^</s//DefaultEncryption IfRequested\n&/' -i /etc/cups/cupsd.conf

# copy the files for the epson printer
COPY etc/cups/printers.conf   /etc/cups
COPY etc/cups/printers.conf.O /etc/cups
RUN mkdir -p /etc/cups/ppd/
COPY etc/cups/ppd/EPSON_Stylus_CX3600.ppd   /etc/cups/ppd/
COPY etc/cups/ppd/EPSON_Stylus_CX3600.ppd.O /etc/cups/ppd/

# Default shell
CMD ["/usr/sbin/cupsd", "-f"]

# docker tag docker-cupsd:latest hcdaniel/docker-cupsd:1.0
# docker login
# docker push hcdaniel/docker-cupsd:1.0
