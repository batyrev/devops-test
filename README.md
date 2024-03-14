## Описание

1. Создаем `Vagrantfile` для запуска виртуальной машины с операционной системой [Ubuntu 20.04](https://app.vagrantup.com/generic/boxes/ubuntu2004).
2. Виртуальная машина поднимается с помощью команды `vagrant up`.
3. Подключаемся по SSH к созданной машине, используя стандартные креды Vagrant (например, через Putty).
4. Устанавливаем ansible: sudo apt update и sudo apt install ansible
5. Используем [Ansible](https://www.ansible.com/) для провижионинга и настройки виртуальной машины, установки Node Exporter (файл с плейбуком - install.yaml, запуск с ansible-playbook install.yaml, можно скачать его с wget из текущего репозитория, можно просто скопировать и вставить с помощью nano/vim)
6. Настраиваем автозапуск [node_exporter](https://github.com/prometheus/node_exporter) на виртуальной машине: создаем systemd unit файл в /etc/systemd/system/node_exporter.service
```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=vagrant
Type=simple
ExecStart=/opt/node_exporter-1.7.0.linux-amd64/node_exporter

[Install]
WantedBy=multi-user.target
```
Далее:
```
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
```
5. Устанавливаем и настраиваем [Grafana](https://grafana.com/) и [Prometheus](https://prometheus.io/) в контейнерах [Docker](https://docs.docker.com/) с помощью [Ansible](https://www.ansible.com/) и [docker-compose](https://docs.docker.com/compose/): используем файл docker-compose.yaml из текущего репозитория, docker-compose up, пароль передаем через переменную среды: export GRAFANA_ADMIN_PASSWORD=your_password
6. Настраиваем [Prometheus](https://prometheus.io/) для сбора метрик от [node_exporter](https://github.com/prometheus/node_exporter) и добавляем его в конфигурацию [Prometheus](https://prometheus.io/) - файл конфигурации prometheus.yaml
7. Добавляем автоматическое создание dashboard [Node Exporter Full](https://grafana.com/grafana/dashboards/1860-node-exporter-full/) в [Grafana](https://grafana.com/) для отображения основных метрик виртуальной машины.
```
1. Заходим в веб-интерфейс Grafana (http://localhost:3000) с использованием учетных данных администратора.
2. Переходим во вкладку "Dashboards" и выбираем "Manage" -> "Import".
3. Вставляем идентификатор панели (1860) в поле "Grafana.com Dashboard" и нажмимаем кнопку "Load".
4. На странице "Import Dashboard" нажмимаем кнопку "Import".
```