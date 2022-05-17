```bash
# kubectl get pods -l dc=UK
# kubectl get pods -l dc=Netherlands
# kubectl get pod -L dc
# kubectl label nodes elliot-02 dc-
# kubectl label nodes --all dc-
# kubectl label node elliot-02 disk=SSD
# kubectl label node elliot-02 dc=UK
# kubectl label node elliot-03 dc=Netherlands
# kubectl label nodes elliot-03 disk=hdd
# kubectl label nodes elliot-03 disk=HDD --overwrite
# kubectl label nodes elliot-02 --list
# kubectl label nodes elliot-03 --list
```