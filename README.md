README

Run up prometheus, grafana and various exporters.

Once configured, a run is done something like:

$ ansible-playbook -i inventory/testing prometheus.yml  -D -K

This uses groups to allocate what hosts gets what part of the monitoring solution, they are mostly self explanatory.

# Group Names
prometheus
alertmanager
grafana
node_exporter
libvirt_exporter
podman_exporter
redfish_exporter
haproxy_exporter

# Tags

There are multiple tags defined to allow for targetted rolling out - they could be broken down a bit more, for example the exporter tag.

* alertmanager
* exporter
* grafana
* prometheus

To only run one of the sections, for example the exporters:

$ ansible-playbook -i inventory/testing prometheus.yml  -D -K --tags exporter

# Redfish Exporter

This relies on a hostvar against the hosts in the redfish_exporter group called ipmi_ip.  If you require a port, just add it here.  The actual exporter runs on the prometheus host.

# Dashboards

Grafana dashboards are stored in files/dashboards, simply place them in the directory and the next run of grafana will populate them out.  They can be edited, but it won't let you save back - but you can export the json.  The workflow for updating these is to put the json back into ansible and roll it back out.  This could probably be improved.

# Alerting

Alertmanager is deployed, but currently doesn't have any alerting rules.  This is a definite TODO.

# TODO

* Alertmanager rules - stub rules file with pointer to extra file people can edit - mount inside alertmanager container
* Move generated ip lists to file_sd_config setup
* Work out how to have different ipmi credentials for redfish exporter
* Better handling of multiple prometheus servers - ie redfish exporter is hard coded to first prometheus host IP
* Better docs and how to use

## Exporters

* Finish haproxy
* Rabbitmq
* Mysql / galera
* Pacemaker
* Rabbitmq
* Openstack
* Ceph

# Notes and References

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
https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/

https://grafana.com/docs/grafana/latest/administration/provisioning/
https://github.com/vegasbrianc/prometheus/blob/master/grafana/provisioning/datasources/datasource.yml
https://grafana.com/docs/grafana/latest/administration/provisioning/#dashboards

https://docs.ansible.com/ansible/latest/galaxy/user_guide.html#installing-collections
community.grafana
https://docs.ansible.com/ansible/latest/collections/community/grafana/index.html


