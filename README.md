# ntp-server
Run NTP Server with Podman Pod

## 1. git clone
```
git clone https://github.com/braveantony/ntp_server.git
```

## 2. Edit NTP Configuration

```
nano ntp_server/Containerfile
```

- 修改 allow ip 範圍

## 3. Build Container Image
```
sudo podman build -t ntp_server:latest -f ntp_server/Containerfile
```

## 4. Run Container
```
sudo podman kube play ntp_server/ntp_server.yaml
```

## 4. Check Pod Status
```
sudo podman ps -a --pod
```
螢幕輸出 :
```
CONTAINER ID  IMAGE                                    COMMAND     CREATED         STATUS         PORTS       NAMES               POD ID        PODNAME
ab2fc7e93236  localhost/podman-pause:4.4.4-1680004800              39 seconds ago  Up 35 seconds              d709efb8d672-infra  d709efb8d672  chrony
d2a616434c9a  quay.io/hahappyman/ntp_server:latest                 35 seconds ago  Up 35 seconds              chrony-chrony       d709efb8d672  chrony
```

## 5. Testing From anthor machine
```
sntpc -n <NTP Server IP>
```
> `sudo apk update; sudo apk add sntpc`

螢幕輸出 :
```
sntpc[4396]: offset=-0.001558, delay=0.000023
```

## 6. Start on boot

```
sudo mkdir -p /etc/containers/systemd/
sudo cp ntp_server/ntp_server.yaml /etc/containers/systemd/
sudo cp ntp_server/chrony.kube /etc/containers/systemd/
sudo systemctl daemon-reload
sudo systemctl start chrony.service
sudo systemctl status chrony.service
```
螢幕輸出 :
```
● chrony.service - Chrony NTP Server Pod
     Loaded: loaded (/etc/containers/systemd/chrony.kube; generated)
     Active: active (running) since Tue 2026-03-31 05:34:44 UTC; 2min 56s ago
   Main PID: 4384 (conmon)
      Tasks: 3 (limit: 4601)
     Memory: 2.0M (peak: 14.8M)
        CPU: 93ms
     CGroup: /system.slice/chrony.service
             ├─4384 /usr/bin/conmon --api-version 1 -c e41f1f2bb10288553544aaff66eb40ee9136a86257ba03ce5caaf18693facb2a -u e41f1f2bb10288553544aaff66eb40ee9136a86257ba03ce5caaf18693facb2a -r /usr/bin/crun -b …
             ├─4389 /usr/bin/conmon --api-version 1 -c 4b92d220754eebb51adc75bb6dac268259b5285b3e3ae545aff8f67f1e9ca50a -u 4b92d220754eebb51adc75bb6dac268259b5285b3e3ae545aff8f67f1e9ca50a -r /usr/bin/crun -b …
             └─4394 /usr/bin/conmon --api-version 1 -c ec1cc0481ac9207a679d1acd91af45b0541cfc2e68c9a94132ba5e53ff36de64 -u ec1cc0481ac9207a679d1acd91af45b0541cfc2e68c9a94132ba5e53ff36de64 -r /usr/bin/crun -b …

Mar 31 05:34:44 core-netowrk-system podman[4350]: 2026-03-31 05:34:44.260792008 +0000 UTC m=+0.245315070 container start ec1cc0481ac9207a679d1acd91af45b0541cfc2e68c9a94132ba5e53ff36de64 (image=localhost/ntp_s…
Mar 31 05:34:44 core-netowrk-system podman[4350]: 2026-03-31 05:34:44.265790495 +0000 UTC m=+0.250313558 pod start 40eeea9c61e851919529ceb6244a99505a0af36cca967414ece44af021ec7856 (image=, name=chrony)
Mar 31 05:34:44 core-netowrk-system chrony[4350]: Pod:
Mar 31 05:34:44 core-netowrk-system chrony[4350]: 40eeea9c61e851919529ceb6244a99505a0af36cca967414ece44af021ec7856
Mar 31 05:34:44 core-netowrk-system chrony[4350]: Container:
Mar 31 05:34:44 core-netowrk-system chrony[4350]: ec1cc0481ac9207a679d1acd91af45b0541cfc2e68c9a94132ba5e53ff36de64
Mar 31 05:34:44 core-netowrk-system systemd[1]: Started chrony.service - Chrony NTP Server Pod.
Mar 31 05:34:44 core-netowrk-system chrony-chrony[4394]: 2026-03-31T05:34:44Z chronyd version 4.1 starting (+CMDMON +NTP +REFCLOCK +RTC +PRIVDROP +SCFILTER +SIGND +ASYNCDNS +NTS +SECHASH +IPV6 -DEBUG)
Mar 31 05:34:44 core-netowrk-system chrony-chrony[4394]: 2026-03-31T05:34:44Z Initial frequency 17.016 ppm
Mar 31 05:34:48 core-netowrk-system chrony-chrony[4394]: 2026-03-31T05:34:48Z Selected source 216.239.35.4 (time.google.com)
Hint: Some lines were ellipsized, use -l to show in full.
```
