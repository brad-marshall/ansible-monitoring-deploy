# Ansible Deployment of Prometheus, Thanos, Grafana and Exporters

Run up prometheus, thanos, minio, grafana and various exporters.

Once configured, a run is done something like:

$ ansible-playbook -i inventory/testing prometheus.yml  -D -K  -e "@groups.yml"

This uses groups to allocate what hosts gets what part of the monitoring solution, they are mostly self explanatory.

To install the required ansible collections:

$ ansible-galaxy collection install -r collections/requirements.yml 

# Groups

The deployment relies on correct allocation of groups to the right hosts, which is done via a set of extra variables we pass through.  If you don't have a group defined,  it won't run.  The critical ones to make sure you have are prometheus, grafana, and whatever exporters you want.  If you want thanos, youalso need thanos (obviously) and minio.  There are plans to allow configuration of an external storage for thanos at some stage.

The thanos deployment is pretty basic so far, consisting of store, receive, query and compact.  There is no HA, but this would be a reasonably easy extension.

## Group Names
prometheus
alertmanager
grafana
node_exporter
libvirt_exporter
podman_exporter
redfish_exporter
haproxy_exporter
minio
thanos
loki
promtail

## Example YAML config

This maps where each role will be deployed, and is also used in template files in the like to consolidate all the details together.

prometheus_group: prometheus
alertmanager_group: alertmanager
grafana_group: grafana
node_exporter_group: node_exporter
libvirt_exporter_group: libvirt_exporter
podman_exporter_group: all
redfish_exporter_group: prometheus
haproxy_exporter_group: haproxy_exporter
thanos_group: thanos
minio_group: minio
loki_group: loki
promtail_group: all

# Tags

There are multiple tags defined to allow for targetted rolling out - they could be broken down a bit more, for example the exporter tag.

* alertmanager
* exporter
* grafana
* prometheus
* minio
* thanos
* loki
* promtail

To only run one of the sections, for example the exporters:

$ ansible-playbook -i inventory/testing prometheus.yml  -D -K --tags exporter

# Redfish Exporter

This relies on a hostvar against the hosts in the redfish_exporter group called ipmi_ip.  If you require a port, just add it here.  The actual exporter runs on the prometheus host.

The redfish password is currently undefined in the repo, you need to pass it in as an extra variable so it is not stored unencrypted, something like:

  $ ansible-playbook -i inventory/testing prometheus.yml -D -K -e "@groups.yaml" -e "redfish_password=$REDFISH_PASS" --tags redfish

You can also pass redfish_user and redfish_job to update those if required, otherwise they default to 'redfish' and 'redfish/openstack' respectively.

This should be improved to store the variable encrypted, but this is a todo.

# Dashboards

Grafana dashboards are stored in files/dashboards, simply place them in the directory and the next run of grafana will populate them out.  They can be edited, but it won't let you save back - but you can export the json.  The workflow for updating these is to put the json back into ansible and roll it back out.  This could probably be improved.

# Alerting

Alertmanager is deployed, and currently has some basic alerting rules, but no notifications.  This is a definite TODO to improve on.

# Thanos

This Thanos deployment has been done using https://blog.ruanbekker.com/blog/2020/02/01/setup-thanos-on-docker-a-highly-available-prometheus/ as a guide.

It initially deploys minio, thanos store, query, compact and reciever components.


# Loki

This deploys loki and promtail, and sets up a connection in grafana to talk to loki.

Useful links in figuring this out are:

* https://grafana.com/docs/loki/latest/clients/promtail/configuration/
* https://github.com/brpaz/logs-with-loki
* https://mpolinowski.github.io/docs/DevOps/Provisioning/2021-04-07--loki-prometheus-grafana/2021-04-07/
* https://open200.com/de/blog/loki-logs/

# Special Variables

## Hostvars

ipmi_ip - used by redfish_exporter to generate list of targets
grafana_group - used by grafana to set group permissions of dashboards etc to something the user can write to

# TODO

* Change thanos storage to be external - ceph-radosgw first
* Better docs and how to use
* Better loki dashboards, include some for logs
  - variable for hostname and cotnainer to show logs?
* Alertmanager rules - stub rules file with pointer to extra file people can edit - mount inside alertmanager container
* Move generated ip lists to file_sd_config setup
* Store redfish credentials encrypted
* Work out how to have different ipmi credentials for redfish exporter
* Better handling of multiple prometheus servers - ie redfish exporter is hard coded to first prometheus host IP
* Consider thanos query-frontend and ruler as advanced setups

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

Thanos

Minio - https://hub.docker.com/r/minio/minio
  - tag RELEASE.2023-01-06T18-11-18Z
Thanos - https://hub.docker.com/r/thanosio/thanos
  - tag 0.30.1
