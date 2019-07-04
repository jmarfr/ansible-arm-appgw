Role Name
=========

Deploy an Azure application gateway

Requirements
------------

N/A

Role Variables
--------------

 * **rg_name**: Name of the resource group
 * **appgw_name**: Name of the application gateway
 * **sku_name**: Name of the application gateway SKU (standard_small | standard_medium | standard_large | waf_medium | waf_large)
 * **sku_tier**: Name of the SKU tier (standard | waf)
 * **nb_instances**: Number of instances which compose the application gateway

  * **appgw_frontend_ip_configurations**: Configuration of the frontend ip.
```yaml
appgw_frontend_ip_configurations:
  - subnet:
     id: "{{ subnet_id}}"
    name: "{{ appgw_name }}_frontend_ip
    private_ip_allocation_method: "dynamic"
```

  * **appgw_frontend_ports**: Configuration of frontend ports.

```yaml
appgw_frontend_ports:
  - port: 80
    name: "{{ appgw_name }}_frontend_port_80
  - port: 8080
    name: '{{ appgw_name }}_frontend_port_8080
```

  * **appgw_backend_address_pools**: Configuration of backend pools.

```yaml
appgw_backend_address_pools:
  - backend_addresses:
    - ip_address: 10.0.0.4
    - ip_address: 10.0.0.5
    name: "{{ appgw_name }}_backend_address_pool
```

  * **appgw_backend_http_settings**: Configuration of http settings

```yaml
appgw_backend_http_settings:
  - port: 80
    protocol: http
    cookie_based_affinity: enabled
    name: "{{ appgw_name }}_appgateway_http_settings_80
  - port: 80
    protocol: http
    cookie_based_affinity: enabled
    name: "{{ appgw_name }}_appgateway_http_settings_80"
```

  * **appgw_http_listeners**: Configuration of appgw http listeners

```yaml
appgw_http_listeners:
  - frontend_ip_configuration: "{{ appgw_name }}_frontend_ip"
    frontend_port: "{{ appgw_name }}_frontend_port_80"
    name: "{{ appgw_name }}_http_listener_80
  - frontend_ip_configuration: "{{ appgw_name }}_frontend_ip"
    frontend_port: "{{ appgw_name }}_frontend_port_8080"
    name: "{{ appgw_name }}_http_listener_8080
```

  * **appgw_request_routing_rules**: Configuration of requests routing rules

```yaml
appgw_request_routing_rules:
  - rule_type: Basic
    backend_address_pool: "{{ appgw_name }}_backend_address_pool"
    backend_http_settings: "{{ appgw_name}}_appgateway_http_settings_80"
    http_listener: "{{ appgw_name }}_http_listener_80"
    name: "{{ appgw_name}}_rule1"
```

  * **appgw_probes** : Configuration of probes

```yaml
appgw_probes:
  - name: "{{ appgw_name }}_probe_80"
    host: myhostname.com
    interval: 5
    path: /
    protocol: http
    timeout: 60
    unhealthy_threshold: 5
```

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yaml
    - hosts: localhost
      connection: local
      vars:
        rg_name: RG_JMA-test-ansible_20190527
        location: westeurope
        vm_offer: CentOS
        vm_publisher: OpenLogic
        vm_sku: 7.6

        appgw_frontend_ports: 
          - port: 80
            name: test_appgw_frontend_port
          - port: 8080
            name: test_appgw_frontend_port_8080

        appgw_frontend_ip_configurations:
          - subnet:
              id: "{{ subnet_id }}"
            name: "{{ appgw_name }}_frontend_ip"
            private_ip_allocation_method: "dynamic"

        appgw_backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ vm_ips.get('jma-ansible-test-1') }}"
              - ip_address: "{{ vm_ips.get('jma-ansible-test-2') }}"

            name: "{{ appgw_name }}_backend_address_pool_1"

        appgw_backend_http_settings:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: "{{ appgw_name }}_appgateway_http_settings"

        appgw_http_listeners:
          - frontend_ip_configuration: "{{ appgw_name }}_frontend_ip"
            frontend_port: "{{ appgw_name }}_frontend_port"
            name: "{{ appgw_name }}_http_listener"

        appgw_request_routing_rules:
          - rule_type: Basic
            backend_address_pool: "{{ appgw_name }}_backend_address_pool_1"
            backend_http_settings: "{{ appgw_name }}_appgateway_http_settings"
            http_listener: "{{ appgw_name }}_http_listener"
            name: "{{ appgw_name }}_rule1"

        appgw_probes:
          - name: "{{ appgw_name }}_probe_80"
            host: myhostname.com
            interval: 5
            path: /
            protocol: http
            timeout: 60
            unhealthy_threshold: 5
      roles:
         - { role: ansible-arm-appgw, vnet_name: "JMA-VNET-1", subnet_name: "subnet2", appgw_name: "test_appgw" }
```
License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
