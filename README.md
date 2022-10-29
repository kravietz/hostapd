Role Name
=========

Role to set up and configure `hostapd` using `systemd.service` that
supports all configuration options.

Requirements
------------

None.

Role Variables
--------------

Interface to run `hostapd` on. This will be used to start `hostapd@wlan1.service` in `systemd`.

```
hostapd_interface: wlan1
```
The following `hostapd_config` dictionary will be converted one-to-one into the hostapd configuration file. Please see [hostapd.conf reference](http://w1.fi/cgit/hostap/plain/hostapd/hostapd.conf) for details on individual options:

```
hostapd_config:
    country_code: GB
    bridge: br0
    interface: "{{ hostapd_interface }}"
    driver: nl80211
    ssid: "MyWLAN"
    hw_mode: g
    channel: 9
    wmm_enabled: 0
    macaddr_acl: 0
    auth_algs: 1
    wpa: 2
    wpa_passphrase: "OhGae6hay0rooRi0poo7"  # protect with ansible-vault
    wpa_key_mgmt: WPA-PSK WPA-PSK-SHA256
    wpa_pairwise: TKIP CCMP
    rsn_pairwise: CCMP
    ctrl_interface: /var/run/hostapd
```

Dependencies
------------

If you are bridging WiFi subnet into LAN, you can also set up bridge using [shibumi.systemd-conf](https://galaxy.ansible.com/shibumi/systemd-conf)
role, as in the following example:

```
systemd_conf_network:
  10-lan:
    - Match:
        - Name: br0
    - Network:
        - Address: '192.168.1.251/24'
        - Gateway: '192.168.1.252'

  20-eth:
    - Match:
        - Name: eth0
    - Network:
        - Bridge: br0       

systemd_conf_netdev:
    10-bridge:
        - NetDev:
            - Name: br0
            - Kind: bridge
```


Example Playbook
----------------

Assuming the above configuration in `host_vars`:

    - hosts: access_point
      roles:
         - kravietz.hostapd
         - shibumi.systemd-conf



License
-------

GPLv3

Author Information
------------------

Paweł Krawczyk https://krvtz.net/