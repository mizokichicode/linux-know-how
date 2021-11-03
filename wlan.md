# wlan

- Ubuntu ServerをLANへ接続する時の設定手順

## 1. デバイス名の確認

- `lo` ではないデバイス名が確認する (WiFiデバイスも搭載されている場合は要確認)

```
% ip link
1: lo: <LOOPBACK,,,>
    link/loopback
2: eno1: <,,,>
    link/ether
```

##　2. 設定

### 1) netplan設定

- `netplan` の設定を追加します

```
% sudo vim /etc/netplan/99_network-conf.yaml
---------------------------------------------------------------------------------------------------
network:
    version: 2
    renderer: networkd
    ethernets:
        eno1:
            dhcp4: false
            dhcp6: false
            addresses: [172.16.1.10/24]
            gateway4: 172.16.1.254
            nameservers:
                addresses: [172.16.1.254]
---------------------------------------------------------------------------------------------------
```

### 2) 設定の適用

```
% sudo netplan apply
```



