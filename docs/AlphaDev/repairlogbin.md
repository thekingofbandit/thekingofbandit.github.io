# Repair Log Bin

### 1. Problem Log Bin not sync after master reset
Problem after mariadb restart, replication not sync
this happen because log bin master corrupt

####fix replication not sync:

1. Reset Slave
2. Manually change to latest log bin
3. Check OMevents; make sure last update field synchronized
