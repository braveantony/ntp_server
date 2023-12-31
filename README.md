# ntp-server
NTP Server on Container

## 1. git clone
```
git clone https://github.com/braveantony/ntp_server.git
```

## 2. Build Container Image
```
sudo podman build -t ntp/ntp_server:latest -f ntp-server/Containerfile
```

## 3. Custom Config

```
nano ntp_server/chrony.conf
```

## 4. Run Container
```
sudo podman run -d                       \
            --name chrony           \
            --network=host          \
            --cap-add SYS_NICE      \
            --cap-add SYS_TIME      \
            --cap-add SYS_RESOURCE  \
            -v $PWD/ntp_server/chrony.conf:/etc/chrony/chrony.conf:ro     \
            localhost/ntp/ntp_server
```

## 5. Self Testing
```
sudo podman exec chrony ntpdate -q $IP
```
> Note: IP 變數為本機 IP

螢幕輸出 :
```
server 192.168.61.143, stratum 2, offset +0.000029, delay 0.02567
 1 Dec 06:55:57 ntpdate[7]: adjust time server 192.168.61.143 offset +0.000029 sec
```

## 6. Testing From anthor machine
```
sntpc -n <NTP Server IP>
```
> `sudo apk update; sudo apk add sntpc`

螢幕輸出 :
```
sntpc[4396]: offset=-0.001558, delay=0.000023
```
