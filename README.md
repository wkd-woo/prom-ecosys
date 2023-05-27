# Grafana Container

prometeus-grafana-alertmanager의 prometheus ecosystem을 docker container로 구성하는 docker-compose입니다.

<br>

1. grafana 일부 플러그인 사용 dependency를 위해
2. 모니터링 에코시스템의 빠른 구축을 위해

container로 패키징 했습니다.

<br>

# Requirements

구성 노드에 docker 환경이 요구됩니다

```
Docker: version 20.10.23+
Docker Compose: version v2.15.0+
```

<br>

# How to use

구성 노드에 git clone을 한 뒤, `docker-compose up -d` 커맨드를 통해 daemon으로 실행합니다.

```shell script
sudo docker-compose up -d
```

<br>

# Details

- 컨테이너 내부/외부 Grafana 포트는 3000:3000 입니다.
- Prometheus는 9000:9000, alertmanager 9093:9093 포트에 구성되어 있습니다.
  - 각자 할당된 포트 사용
  - 프로세스로 취급하고 사용하면 됩니다.
- 컨테이너 내부 리소스를 공유하는 volume이 구성되어 있습니다.
  - `volume` 디렉토리 이하에 **컨테이너 리소스를 공유**합니다.
    - _ex) `conf/grafana.ini` 를 튜닝하여 그라파나 구성을 설정할 수 있습니다._
    - `prom/config/prometheus.yml`, `prom/config/rule.yml` 수정하여 prometheus job scrapping, alert rule 설정 가능합니다.

## notice !

- Grafana 볼륨 디렉터리, 파일 소유권한을 그라파나(472)로 유지해야 합니다.
  - (최소 conf/, volume/ 이 두 개는 유지해야 함)
  - Grafana image의 Linux user가 grafana(472):grafana(472) 로 설정되어 있음
  - Grafana 가 제대로 동작하지 않는다면, 파일 소유 권한이 원인일 확률이 높음
- Grafana 내장 DB(SQLite) 쓰기, 읽기 권한이 Grafana에 있어야 하기 때문

<br>

## version

```
grafana/grafana:7.5.13-ubuntu
prom/prometheus:latest
bitami/alertmanager:latest
grafana/grafana-image-renderer:3.5.0
```

- Grafana v8.0+ 부터 Apache License 2.0 -> AGPLv3 라이선스 변경
- 라이선스 이슈가 없을 경우, latest version 변경 가능

<br>

## plugins

패키징된 플러그인 목록입니다. [Grafana Labs에서 지원하는 Plugins](https://grafana.com/grafana/plugins/?type=panel) 추가 가능합니다.

### [Grafana-image-renderer](https://grafana.com/grafana/plugins/grafana-image-renderer/)

- containered
- 그라파나 패널 이미지 렌더링 플러그인
  - alert에 이미지 첨부 가능
- dependency: Grafana >= 7.0.0

<br>

### [vonage-status-panel](https://grafana.com/grafana/plugins/vonage-status-panel/)

![](grafana/volume/plugins/vonage-status-panel/img/environment_snapshot.png)

- not containered
- 장치 status 시각화 플러그인
- dependency: Grafana >= 3.0.0

<br>

### [grafana-boom-table](https://grafana.com/grafana/plugins/yesoreyeram-boomtable-panel/)

![](grafana/volume/plugins/yesoreyeram-boomtable-panel/src/img/panels-fa.png)

![](grafana/volume/plugins/yesoreyeram-boomtable-panel/src/img/panel.png)

- not containered
- Graphite, InfluxDB, Prometheus, Azure Monitor 다중 열 시각화 제공 플러그인
- dependency: Grafana >= 4.x.x
