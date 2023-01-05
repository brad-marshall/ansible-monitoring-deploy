README

Run up prometheus, grafana and various exporters.

TODO:
* Redfish exporter - use hostvars on servers to set IPMI address
* Alertmanager rules - stub rules file with pointer to extra file people can edit - mount inside alertmanager container
* Move generated ip lists to file_sd_config setup
* Work out how to have different ipmi credentials for redfish exporter
* Better handling of multiple prometheus servers - ie redfish exporter is hard coded to first prometheus host IP
* Better docs and how to use

Podman

podman run -d -p 9090:9090 --name prometheus quay.io/prometheus/prometheus
podman run -d -p 9100:9100 --name node-exporter quay.io/prometheus/node-exporter
docker run -p9177:9177 -v /var/run/libvirt:/var/run/libvirt alekseizakharov/libvirt-exporter:latest

Grafana

https://hub.docker.com/r/grafana/grafana
docker run -d --name=grafana -p 3000:3000 grafana/grafana

Podman
podman run -e CONTAINER_HOST=unix:///run/podman/podman.sock -v /run/podman/podman.sock:/run/podman/podman.sock -u root --security-opt label=disable quay.io/navidys/prometheus-podman-exporter

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


