# wb19-20-terraform

Aquí está la documentación para hetzner con terraform
https://registry.terraform.io/providers/hetznercloud/hcloud/latest/docs

Creamos un tocken en hetzner de escritura.

> Creación de redes
> 
> Ahora veremos como configurar redes privadas y asignar IPs privadas a un servidor.
> En primer lugar elimina la configuración anterior de .tf y añade la siguiente:
> Pregunta 1: ¿Qué ha sucedido?

Ha creado un servidor, una red y dentro una subred y una interfaz adjunta al servidor.
```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # hcloud_network.mynet will be created
  + resource "hcloud_network" "mynet" {
      + id       = (known after apply)
      + ip_range = "10.0.0.0/8"
      + name     = "my-net"
    }

  # hcloud_network_subnet.foonet will be created
  + resource "hcloud_network_subnet" "foonet" {
      + gateway      = (known after apply)
      + id           = (known after apply)
      + ip_range     = "10.0.1.0/24"
      + network_id   = (known after apply)
      + network_zone = "eu-central"
      + type         = "cloud"
    }

  # hcloud_server.node1 will be created
  + resource "hcloud_server" "node1" {
      + backup_window = (known after apply)
      + backups       = false
      + datacenter    = (known after apply)
      + id            = (known after apply)
      + image         = "debian-9"
      + ipv4_address  = (known after apply)
      + ipv6_address  = (known after apply)
      + ipv6_network  = (known after apply)
      + keep_disk     = false
      + location      = (known after apply)
      + name          = "node1"
      + server_type   = "cx11"
      + status        = (known after apply)
    }

  # hcloud_server_network.srvnetwork will be created
  + resource "hcloud_server_network" "srvnetwork" {
      + id          = (known after apply)
      + ip          = "10.0.1.5"
      + mac_address = (known after apply)
      + network_id  = (known after apply)
      + server_id   = (known after apply)
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

hcloud_network.mynet: Creating...
hcloud_server.node1: Creating...
hcloud_network.mynet: Creation complete after 1s [id=1121638]
hcloud_network_subnet.foonet: Creating...
hcloud_network_subnet.foonet: Creation complete after 1s [id=1121638-10.0.1.0/24]
hcloud_server.node1: Creation complete after 8s [id=11935652]
hcloud_server_network.srvnetwork: Creating...
hcloud_server_network.srvnetwork: Creation complete after 2s [id=11935652-1121638]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.
```

## 8. Outputs

Las salidas son la forma que tiene Terraform de mostrarte cualquier información sobre lo que se hace. Podría proporcionar información diferente, así que sólo mostraré las cosas muy básicas aquí.
Lo que veremos después de crear nuestra infraestructura con Terraform incluirá:

La dirección IP del balanceador de carga que puede usar para conectarse a los VM y ver lo que responden.
El estado de las máquinas virtuales creadas. Suponemos que está "funcionando" si todo está bien.
Direcciones IP de las máquinas virtuales creadas, muy útiles si necesitas conectarte a ellas por SSH.
Una vez llegados a este punto haremos un terraform init. Si todo ha sido satisfactorio realizaremos un terramorm plan y miramos que todo esté correctamente.
 
Finalmente lo ejecutamos: `terraform apply`

> Pregunta 2: ¿Qué muestra el output?

Hay que crear una key nueva con ssh-keygen y después lanzar en la carpeta:
```
terraform init
terraform plan
```
Si todo está bien aplicar
`terraform apply`

```
hcloud_ssh_key.default: Creating...
hcloud_ssh_key.default: Creation complete after 1s [id=3531720]
hcloud_server.web[1]: Creating...
hcloud_server.web[0]: Creating...
hcloud_server.web[2]: Creating...
hcloud_server.web[1]: Creation complete after 9s [id=11936364]
hcloud_server.web[2]: Creation complete after 10s [id=11936366]
hcloud_server.web[0]: Creation complete after 10s [id=11936365]
hcloud_server_network.web_network[0]: Creating...
hcloud_volume_attachment.web_vol_attachment[2]: Creating...
hcloud_volume_attachment.web_vol_attachment[0]: Creating...
hcloud_volume_attachment.web_vol_attachment[1]: Creating...
hcloud_server_network.web_network[2]: Creating...
hcloud_server_network.web_network[1]: Creating...
hcloud_load_balancer.web_lb: Creating...
hcloud_load_balancer.web_lb: Creation complete after 2s [id=300780]
hcloud_load_balancer_network.web_network: Creating...
hcloud_load_balancer_service.web_lb_service: Creating...
hcloud_server_network.web_network[1]: Creation complete after 3s [id=11936364-1121668]
hcloud_volume_attachment.web_vol_attachment[2]: Creation complete after 3s [id=11300120]
hcloud_volume_attachment.web_vol_attachment[0]: Creation complete after 3s [id=11300121]
hcloud_load_balancer_service.web_lb_service: Creation complete after 2s [id=300780__80]
hcloud_volume_attachment.web_vol_attachment[1]: Creation complete after 5s [id=11300122]
hcloud_load_balancer_network.web_network: Creation complete after 4s [id=300780-1121668]
hcloud_server_network.web_network[2]: Creation complete after 6s [id=11936366-1121668]
hcloud_server_network.web_network[0]: Creation complete after 7s [id=11936365-1121668]

Apply complete! Resources: 13 added, 0 changed, 0 destroyed.

Outputs:

lb_ipv4 = "167.233.15.68"
web_servers_ips = {
  "web-server-0" = "157.90.234.132"
  "web-server-1" = "162.55.177.190"
  "web-server-2" = "116.203.214.159"
}
web_servers_status = {
  "web-server-0" = "running"
  "web-server-1" = "running"
  "web-server-2" = "running"
}
```

> Pregunta 3: Finalmente pon la URL de tu balancer en el navegador. ¿Qué observamos?

Vemos la salida de nginx que hemos creado en el fichero user_data.yml

> Pregunta 4: Realiza los cambios pertinentes para aprovisionar 2 servidores más sin apagar la infraestructura.

Modificamos en el fichero variables.tf la variable instances y en lugar de 3 ponemos 5.
Comprobamos y aplicamos
```
terraform plan
terraform apply
```
Esta es la salida:
```
Apply complete! Resources: 8 added, 1 changed, 0 destroyed.

Outputs:

lb_ipv4 = "167.233.15.68"
web_servers_ips = {
  "web-server-0" = "157.90.234.132"
  "web-server-1" = "162.55.177.190"
  "web-server-2" = "116.203.214.159"
  "web-server-3" = "162.55.177.105"
  "web-server-4" = "162.55.177.99"
}
web_servers_status = {
  "web-server-0" = "running"
  "web-server-1" = "running"
  "web-server-2" = "running"
  "web-server-3" = "running"
  "web-server-4" = "running"
}
```

> Pregunta 5: Crea un nuevo proyecto e inicializa un nuevo proyecto de terraform que contendrá :
> • Dos load balancer
> • Tres frontales web con wordpress
> • Tres backend galera cluster


> Pregunta 6: Crea e inicializa un nuevo proyecto de terraform que contendrá :
> • Dos load balancer
> • Tres frontales web con magento y redis cluster
> • Tres backend galera cluster
