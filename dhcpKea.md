## KEA
# 1. Instalación
Para instalar kea escribiremos el siguiente comando:  
`sudo apt install kea-dhcp4-server kea-admin`

# 2. Configuración
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
Con esto
