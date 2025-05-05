# DNS con Bind9
Para configurar nuestro servido con un servicio DNS propio usaremos BIND9
## Instalación
Para instalar Bind9 en nuestro servidor usaremos el siguiente comando:  
`apt install bind9 bind9utils bind9-doc dnsutils`  
esto nos instalara bind9 y otras dependencias que os haran falta.
## Configuración
Para configurar nuestro servicio DNS deberemos ir al directorio con todos sus ficheros de configuración, este se haya en `/etc/bind`.  
El primer fichero que deberemos tocas es `named.conf.local`, que es donde indicaremos las zonas de nuestro servicio DNS.  
`nano /etc/bind/named.conf.local`  
Teniendo en cuenta los siguientes datos:
- Nombre del dominio: Skletin5.com
- ip de nuestro servidor de dominio: 192.168.100.1  

Aqui dentro configuraremos lo siguiente:  
```
zone "Skletin5.com" {
        type master;
        file "/etc/bind/db.Skletin5.com;
};

zone "100.168.192.in-addr.arpa" {
        type master;
        file "/etc/bind/db.100.168.192";
};
```
Bien, le hemos indicado al servidor el nombre de la zona directa e inversa, ahora deberemos configurar cada una de ellas.  

Para la zona directa deberemos copiar un fichero ya existente con el nombre que hemos indicado anteriormente, que es `db.local`:
`cp /etc/bind/db.local /etc/bind/db.Skletin5.com`  
Deberemos cambiar los datos que contienen:  
`nano /etc/bind/db.Skletin5.com`
