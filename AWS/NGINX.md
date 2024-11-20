# comandos para comprobar si esta arrancado NGINX

```linux
ps -elf |grep unit

systemctl daemon-reload

systemclt status unit
```
# para arrancar 


```linux
sudo systemctl start unit

```
Arranca tres procesos 

which unitd
command -v unid

la versión
unitd --version

ves que tiene el socket en var/run/control.unit.sock

sudo curl --unix-socket /var/run/control.unit.sock localhost

no exponer publicamente el endpoint

ayuda   unitd -h

tenemos para realizardo primero parar el servicio
sudo  systemctl stop unit

unitd --control 127.0.0.1 port 9000

luego nos permitirá realizar curl localhost:9000
sudo unitd --control 127.0.0.1:9000

tambien se puede cambiar si haces  sudo systemctl status unit
nos muestra desde donde carga el servicio
/lib/systemd/system/unit.service

configuramos
curl localhost:9000/config

o bien  sudo apt install jq - es una herramienta para trabajar con json

crea un fichero unit.conf.json  con las opciones de listeners,
{
    "listeners":{
        "*:80":{
        "pass": "routes"
        }
    },
    "routes":[
        {"action":{"return": 200}}
    ],
    "applications":{}

}

cat unit.conf.json  |curl -X PUT -d0 localhost:9000/config
curl -v localhost  y ves que esta configurado

## configurar un servidor NGINX

para configurarlo  tendremos que tener en cuenta el punto donde vamos a exponer la web.
aqui propone dentro de cd /srv  servicios crear el www
crear aqui el fichero index.html de bienvenida
creamos el fichero de configuracion  unit.conf.json 
y ejecutamos
cat unit.conf.json  |curl -X PUT -d0 localhost:9000/config

## modulos
 si realizamos unitd --version  nos indica donde se instalan los modulos
 ls -lah /usr/lib/unit/modules