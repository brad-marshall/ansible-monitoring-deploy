README

Run up prometheus, grafana and various exporters

podman run -d -p 9090:9090 --name prometheus quay.io/prometheus/prometheus
podman run -d -p 9100:9100 --name node-exporter quay.io/prometheus/node-exporter
docker run -p9177:9177 -v /var/run/libvirt:/var/run/libvirt alekseizakharov/libvirt-exporter:latest

https://hub.docker.com/r/grafana/grafana
docker run -d --name=grafana -p 3000:3000 grafana/grafana

Grafana

# CLI Config

https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/

# Add a data source
https://grafana.com/docs/grafana/latest/administration/provisioning/
https://github.com/vegasbrianc/prometheus/blob/master/grafana/provisioning/datasources/datasource.yml

https://grafana.com/docs/grafana/latest/administration/provisioning/#dashboards

# Ansible collections

## Installing

https://docs.ansible.com/ansible/latest/galaxy/user_guide.html#installing-collections

## Grafana Collection

community.grafana

https://docs.ansible.com/ansible/latest/collections/community/grafana/index.html


