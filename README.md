# ansible-role-coredns-docker
Ansible role for deploying a coredns docker container for internal home network usage, it is opinionated to use DNS zone for static hosts and pull CNAME configurations from ansible VARS.  




# requirements
- ubuntu 24.04. It might work in other ubuntu versions but has been tested with ubuntu 24.04. Other versions like 18.04 and 20.04 have been reported to work
- docker: Needs docker installed in the server


# Configuration Variables

This role configuration variables and configuration data structures are compatible with ansible galaxy role fcastello.dhcpd_docker for a DNS/dhcp home router solution

```yaml
coredns_container_name: coredns # docker container name
coredns_version: "1.11.3" # coredns version
coredns_container_image: "coredns/coredns:{{ coredns_version }}" # coredns image name
coredns_container_recreate: false # variable to force recreate container if nothing has changes

coredns_config_dir: /data/coredns # Host directory where coredns configurations will be stored

# forward zones list, multiple zones can be forwarded and . zone is the wildcard and will alow recurse DNS to forward servers
coredns_forward_zones:
  - name: "." # Zone Name
    dns_servers: "1.1.1.1 8.8.8.8 1.0.0.1 8.8.4.4" # Separated by a space, will round robin the servers
  - name: "home.local" 
    dns_servers: "192.168.1.53" # Separated by a space


##### You can defined custom DNS zones. The static hosts stucture is shared with the DHCP static hsots structure.
coredns_zones:
 - name: fcast.ar
## Static hosts structure is shared with the DHCP service so they are defined in the same place needs to be part of the host
   static_hosts:
     - name: myhost # Mandatory, Hostname (must be unique)
       ip: 192.168.1.3 # Mandatory, Fixed IP address
       mac: ab:cd:ef:01:02:03 # Mandatory, Mac Address of the device used for DHCP, for coredns this can be ommited
       ttl: 60 # time to live for DNS
## The records variables are for custom records that are not defined in static hosts
## Currently they support A records and CNAME. Other records might need some work
   dns_records:
     - name: myhost2 # Mandatory, Hostname (must be unique)
       value: 192.168.1.3 # Mandatory, Value od the DNS record
       type: A # Mandatory, Typo of record. Currently SUpports CNAME A and AAAA records
       ttl: 60
     - name: router
       value: myhost2.home.local.
       type: CNAME
       ttl: 60
```


# Limitations
- It is opinionated to generate configuration to special needs
- Runs as network mode host so network isolation is not possible

# To Do
- Make it work not only in network mode host
- Add a way to bind to specific interfaces


# Disclaimer

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.