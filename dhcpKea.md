# KEA
## 1. Instalación
Para instalar kea escribiremos el siguiente comando:  
`sudo apt install kea-dhcp4-server kea-admin`

## 2. Configuración
Para configurar nuestro Kea deberemos trabajar en el directorio `/etc/kea`. Aquí hallaremos el fichero `kea-dhcp4.conf` que usaremos para configurar nuestra red usando nano.  
`nano /etc/kea/kea-dhcp4.conf` 

Para esta practica tendremos en cuenta los siguiente datos:  
- Interfaz: enp0s3
- Ip de la red: 192.168.100.0/24
- Ip del servidor: 192.168.100.1
- Nombre dominio DNS: dominio-100.test (Por lo pronto)
- Dirección DNS: 9.9.9.9 (Por lo pronto)  

```json
{
  "Dhcp4:{
    "interfaces-config":{
      "interfaces":[
        "enp0s3"
      ],
      "dhcp-socket-type":"raw"
    },
    "reservations-global":false,
    "reservations-out-of-pool":true,
    "valid-lifetime":4000,
    "renew-timer":1000
    "rebind-timer":2000,
    "subnet4":[
      {
        "subnet": "192.168.100.0/24",
        "match-client-id": false,
        "option-data":[
          {
            "name":"routers",
            "data":"192.168.100.1"
          },
          {
            "name":"domain-name-servers",
            "data":"9.9.9.9"
          },
          {
            "name":"domain-name",
            "data":"dominio-100.test"
          }
        ],
        "pools":[
          {
            "pool":"192.168.100.100-192.168.100.199"
          }
        ]
      }
    ],
    "loggers":[
      {
        "name":"*"
        "severity":"Debug"
      }
    ]
  }
}
```
Con esto tendriamos la base de nuestro servicio dhcp.  

Podemos agregar tambien ips reservadas por dirección MAC:  
```json
"reservations":[
  {
    "hw-address":"Dirección mac del cliente"
    "ip-address":"Dirección ip reservada"
  }
]
```
Esta opción debe hallarse dentro de `subnet4` y la ip debe estar fuera del pool de ips dinamicas.

## 3. Reinicio
Tras configurar nuestro servidor dhcp deberemos reiniciar el servicio para aplicar la configuración.  
`apt restart kea-dhcp4-server`  
Podemos comprobar que funciona con `status` y si nuestro cliente recibe una ip dentro del pool o la asignada en una reserva.

## 4. Enrutamiento
Deberemos configurar un enrutamiento en nuestro servidor si queremos que los cliente tengan acceso a internet. Existen dos metodos pero en ambos hay que habilitar el forwarding, para ello no vamos al fichero `/etc/sysctl.conf` con nano y buscaremos la linea `#net.ipv4.ip_forwarding=1` y la descomentamos. Guardamos y ejecutamos el comando `sysctl -p`. Tras esto podemos seguir uno de los sigueinte pasos

### Iptables
Deberemos ejecutar los sigueintes comando uno tras uno para establecer el enrutamiento:  
`iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERDE`  
`iptables -A FORWARD -i enp0s8 -o enp0s3 -j ACCEPT`  
`iptables -A FORWARD -i enp0s3 -o enp0s8 -m state --state RELATED, ESTABLISHED -j ACCEPT`  
Esta configuración por defecto no se guarda si reiniciamos el servidor, por lo que deberemos hacerlo permanente con los sigientes comandos:  
`apt install iptables-persistent`  
`netfilter-persistent save`  
`netfilter-persistent reload`  

### Nftables
