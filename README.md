aspera_server
=============

Ansible role which helps to install and configure Aspera Enterprise Server,
Aspera Connect Server and Aspera FASPex Server.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Examples
--------

```yaml
---

# Example of how to use the role with default parameters
- hosts: myhost1
  vars:
    # Login information for the web site to be able to download the package
    aspera_server_download_user: myuser
    aspera_server_download_pass: myP4ssw0rd
    # Server license
    aspera_server_lic: |
      VGhpcyBpcyBhbiBleGFtcGxlLiBUaGlzIGlzIGFuIGV4YW1wbGUuIFRoaXMgaXMgYW4gZXhhbXBs
      ZS4gVGhpcyBpcyBhbiBleGFtcGxlLiBUaGlzIGlzIGFuIGV4YW1wbGUuIFRoaXMgaXMgYW4gZXhh
      ...
      bXBsZS4gVGhpcyBpcyBhbiBleGFtcGxlLiBUaGlzIGlzIGFuIGV4YW1wbGUuIFRoaXMgaXMgYW4g
      ZXhhbXBsZS4gVGhpcyBpcyBhbiBleGFtcGxlLiA=
  roles:
    - aspera_server

# Example of how to alter the default configuration
- hosts: myhost2
  vars:
    # Login information for the web site to be able to download the package
    aspera_server_download_user: myuser
    aspera_server_download_pass: myP4ssw0rd
    # Server license
    aspera_server_lic: |
      VGhpcyBpcyBhbiBleGFtcGxlLiBUaGlzIGlzIGFuIGV4YW1wbGUuIFRoaXMgaXMgYW4gZXhhbXBs
      ZS4gVGhpcyBpcyBhbiBleGFtcGxlLiBUaGlzIGlzIGFuIGV4YW1wbGUuIFRoaXMgaXMgYW4gZXhh
      ...
      bXBsZS4gVGhpcyBpcyBhbiBleGFtcGxlLiBUaGlzIGlzIGFuIGV4YW1wbGUuIFRoaXMgaXMgYW4g
      ZXhhbXBsZS4gVGhpcyBpcyBhbiBleGFtcGxlLiA=
    # Configure the server
    aspera_server_conf_default:
      # Default authorization
      - authorization:
          - value: allow
          - token:
              - encryption_key: MyTopSecretEncryptionKeyForAspera
      # Default user location and permissions
      - file_system:
          - access:
              - paths:
                  - path:
                      - absolute: /home/$(name)
                      - read_allowed: "true"
                      - dir_allowed: "true"
                      - write_allowed: "false"
    # List of users
    aspera_server_conf_aaa__users:
      - user:
          - name: aspera
    # Insert the list of users into the realm
    aspera_server_conf_aaa:
      - realms:
          - realm:
              - users: "{{ aspera_server_conf_aaa__users }}"
  roles:
    - aspera_server
```


Role variables
--------------

Variables used by the role is as follows:

```yaml
# URL to the RPM
aspera_server_download_url: https://download.asperasoft.com/download/sw/entsrv/3.8.0/aspera-entsrv-3.8.0.154152-linux-64.rpm

# Download user name (must be provided by the user)
aspera_server_download_user: null

# Download user pasword (must be provided by the user)
aspera_server_download_pass: null

# Dependency packages
aspera_server_deps:
  - perl-Digest-MD5
  - perl-Data-Dumper

# Service names
aspera_server_services:
  - asperanoded

# Location of temp file (used to download the RPM)
aspera_server_tmp: /tmp

# Location of the license file
aspera_server_lic_file: /opt/aspera/etc/aspera-license

# License (must be provided by the user)
aspera_server_lic: null


# Location of the configuration file aspera.conf
aspera_server_conf_file: /opt/aspera/etc/aspera.conf

# Values of the options of the central_server block
aspera_server_conf_central_server_port: 40001
aspera_server_conf_central_server_address: 127.0.0.1

# Default options of the central_server block
aspera_server_conf_central_server__default:
  - port: "{{ aspera_server_conf_central_server_port }}"
  - address: "{{ aspera_server_conf_central_server_address }}"

# Custom options of the central_server block
aspera_server_conf_central_server__custom: []

# Final options of the central_server block
aspera_server_conf_central_server: "{{
  aspera_server_conf_central_server__default +
  aspera_server_conf_central_server__custom }}"

# Values of the default config options
aspera_server_conf_version: 2
aspera_server_conf_default: ""
aspera_server_conf_aaa: ""

# Default config options
aspera_server_conf__default:
  - ^version: "{{ aspera_server_conf_version }}"
  - central_server: "{{ aspera_server_conf_central_server }}"
  - default: "{{ aspera_server_conf_default }}"
  - aaa: "{{ aspera_server_conf_aaa }}"

# Custom config options
aspera_server_conf__custom: []

# Final config
aspera_server_conf:
  - CONF: "{{
      aspera_server_conf__default +
      aspera_server_conf__custom }}"
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)
- [`users`](https://github.com/jtyr/ansible-users) role (optional)
- [`ssh`](https://github.com/jtyr/ansible-ssh) role (optional)


License
-------

MIT


Author
------

Jiri Tyr
