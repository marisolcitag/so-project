**1.**
# Miniproyecto Sistemas Operativos

**Integrantes:** Cristian Osorio y Marisol Giraldo

**Códigos:** A00056436 y A00246380

**Correos:** crisosotre@hotmail.com y marisol.giraldo@correo.icesi.edu.co

**Curso:** Sistemas Operativos

**Tema:**  LXC/LXD Containers

**Profesor:** Daniel Barragán

**Repositorio GITHUB =>** https://github.com/marisolcitag/so-project.git

# OBJETIVOS:
- Conocer las características principales de la virtualización por medio de contenedores LXC/LXD.                                       
- Virtualizar servicios empleando contenedores LXC/LXD.                                                                                 
- Realizar configuraciones sobre sistemas operativos para el acceso a servicios como: network, load balancing.

**2.**
# DESCRIPCIÓN:
Con la realización de este Readme, se mostrará el despliegue de dos servidores web y un balanceador de carga por medio de contenedores LXC/LXD. Se empleará la máquina virtual ejecutando el sistema operativo Ubuntu 16.04.4 LTS (xenial).
A continuación se muestra el esquema para el despliegue:
![diagrama](https://user-images.githubusercontent.com/35766585/40820419-03d94e82-6526-11e8-8628-6689aaede553.png)

**3.**
# INSTALACIÓN SISTEMA OPERATIVO UBUNTU 16.04.4 LTS (xenial)
1. Se descarga la imagen ISO de UBUNTU 16.04.4 LTS (xenial) del enlace http://releases.ubuntu.com/16.04/
2. Para realizar la instalación del SO UBUNTU 16.04.4 LTS (xenial), utilizamos la máquina virtual ORACLE VIRTUAL BOX, se adjuntó la Imagen ISO descargada en la opción de almacenamiento, se configuró el espacio en memoria, en disco duro, un procesador de 4 nucleos y    las configuraciones de red básicas: ADAPTADOR 1 se configuro como NAT y ADAPTADOR 2 como Adaptador Puente.
   ![procesador 4 nucleos](https://user-images.githubusercontent.com/35766585/40820856-ac35aba0-6528-11e8-8122-c36ea735b066.png)
   ![isoubuntu](https://user-images.githubusercontent.com/35766585/40820959-4cf850c4-6529-11e8-94a7-cdbe1472c082.png)
   ![nat](https://user-images.githubusercontent.com/35766585/40820967-5800eb7a-6529-11e8-8f78-8814457169cc.png)
   ![adaptadorpuente](https://user-images.githubusercontent.com/35766585/40820974-6a44b636-6529-11e8-96af-91dbb2b227cd.png)

**4.**
# INSTALACIÓN DE LXC/LXD CON PERMISOS PARA EL USUARIO OPERATIVOS.
La guia tutorial How to Set Up and Use LXD on Ubuntu 16.04 que se encuentra en el enlace: https://www.digitalocean.com/community/tutorials/how-to-set-up-and-use-lxd-on-ubuntu-16-04, menciona respecto a la configuración de LXD, que se encuentra previamente instalado en Ubuntu, pero necesita ser configurado de manera apropiada para poder ser utilizado en un servidor. Se puede configurar una cuenta para manejar contenedores y luego configurar el tipo de almacenamiento backend para almacenar los contenedores y configurar la red.

1. Se crea el usuario operativos
  ```
  usermod -aG sudo operativos
  ```
2. Se adiciona el usuario a el grupo lxd, asi es posible usar el administrador de todos los contenedores.
  ```
  $ sudo usermod --append --groups lxd operativos
  ```
3. Se cierra sesión y se loguea de nuevo para actualizar los cambios. Una vez se loguea de nuevo, se puede iniciar a configurar el LXD.

4. Se actualiza la lista de paquetes disponibles y sus versiones.
  ```
  $ sudo apt-get update
  $ sudo apt-get install zfsutils-linux
  ```
  
5. Ahora se puede configurar el LXD. Para arrancar el proceso de iniciación de LXD se ejecuta el comando:
  ```
  $ sudo lxd init
  ```
  
6. El sistema solicita que se especifique los detalles para el backend de almacenamiento. Una vez se complete esa configuración,se  configurará la red para los contenedores.
  * En primer lugar es necesario crear un storage pool. Un **Storage Pool** es un conjunto de discos en el que el servidor de Data Protection Manager (DPM) almacena réplicas, instantáneas y registros de transferencia. Los storage pool pueden configurarse en diferentes tamaños y proporcionar una serie de beneficios, que incluyen mejoras de rendimiento, administración y protección de datos.
  ![comando5](https://user-images.githubusercontent.com/35766585/40866288-89579f04-65c2-11e8-8034-b72f2997277e.png)
   
  * Luego se le pedirá el nombre de backend de almacenamiento, y el sistema dará dos opciones: dir o zfs. 
  La opción **dir** le dice a LXD que almacene contenedores en directorios en el sistema de archivos del servidor. 
  La opción **zfs** usa el sistema de archivos combinados ZFS y el administrador de volúmenes lógicos. Al usar zfs, obtenemos eficiencia de almacenamiento y una mejor capacidad de respuesta. Por ejemplo, si creamos diez contenedores a partir de la misma imagen de contenedor inicial, todos usan el espacio de disco de una sola imagen de contenedor. A partir de ese  
   momento, solo se almacenarán sus cambios en la imagen del contenedor inicial en el backend de almacenamiento.
   
   * En este proyecto usaremos la opción **zfs** 
  ![comando6](https://user-images.githubusercontent.com/35766585/40866291-8c6c9f5a-65c2-11e8-832f-3905027d0817.png)
  * Se crea el pool **zfs** y se le asigna el nombre **lxd** al pool
  ![comando7](https://user-images.githubusercontent.com/35766585/40866293-909e9222-65c2-11e8-93c9-5babd8fe0ff2.png)
  
  * El sistema solicitará si se desea utilizar un dispositivo de bloque existente.
  ![comando8](https://user-images.githubusercontent.com/35766585/40866301-96a00048-65c2-11e8-967e-35b225108812.png)
  
  * Si selecciona la opción YES debe decirle al LXD donde encontrar este dispositivo. Si dice NO, LXD  usará un directorio pre-asignado para el almacenamiento de los contenedores. Para este proyecto se selecciono la opción NO y se asigno el tamaño por defecto 15.
   ![comando9](https://user-images.githubusercontent.com/35766585/40866346-d3650c08-65c2-11e8-8901-f8ac4c5fff50.png)
 
7. Una vez se configura el backend de almacenamiento, se configura la red para LXD
  ![comando10](https://user-images.githubusercontent.com/35766585/40866356-e196b772-65c2-11e8-89fd-441f38abf236.png)
  
   Aparece la siguiente pantalla, donde se confirma que se quiere configurar el puente de red.
    ![comando11](https://user-images.githubusercontent.com/35766585/40824801-11478d58-653b-11e8-8d2f-6d1ba9a6cd30.png)
  
    Se realizan las siguientes configuraciones:
  * Se preguntará al usuario por el nombre del puente. Se aceptará el valor por defecto.
  * Se preguntará  la configuración para IPv4 y IPv6. Escogeremos solo IPv4.
  * Cuando se pregunte sobre la configuracion subnet IPv4, se escogera YES. El sistema informará sobre una configuración random de   subnet.Se selecciona OK para continuar.
  * Se solicitará una dirección IPv4, se acepta el valor por defecto.
  * Se solicitará una mascara valida, se acepta el valor por defecto.
  * Se solicita la primera dirección DHCP, se acepta el valor por defecto. Se hace lo mismo para la ultima dirección DHCP.
  * Se selecciona YES cuando pregunta hacer NAT al tráfico IPv4.
  * Cuando pregunta la configuración de subnetting para IPv6, seleccionar NO.
  
8. Se podrá ver la siguiente salida una vez la configuración de red se complete.
   ![comando12](https://user-images.githubusercontent.com/35766585/40866425-2ae0fa8c-65c3-11e8-82df-a7291755d125.png)

**5.**
# CREACIÓN DE CONTENEDORES CON SERVICIO WEB
   Si se configuro de manera exitosa el LXD; el primer contenedor esta listo para ser creado y administrado.
1. El siguiente comando se utiliza para verificar los contenedores instalados y disponibles.
  ![comando13](https://user-images.githubusercontent.com/35766585/40825881-926b37ba-653e-11e8-971d-da1ee312a1cb.png)
  ![comando1](https://user-images.githubusercontent.com/35766585/40826046-0ea57b4c-653f-11e8-9cf1-167ba0771a9a.png)
  
2. Se crea e inicia un contenedor Ubuntu 16.04 con el nombre webserver1.
  ```
  lxc launch ubuntu:x webserver1
  ```
3. Como es la primera vez que se crea un contenedor, este comando descarga la imagen del contenedor desde internet por lo que tiende a demorarse de segundos a minutos, sin embargo, la próxima vez en que se cree un contenedor, se creará mas rapidamente. Cuando se haya creado se verá la siguente salida en pantalla:
  
![lxc launch webserver1](https://user-images.githubusercontent.com/35766585/40880616-fb195896-6679-11e8-8e6b-656ab5c5f234.PNG)

4. Ahora que el contenedor esta ejecutandose, se usa el comando lxc list para mostrar la información al respecto.
  ![comando4](https://user-images.githubusercontent.com/35766585/40827190-8148bb2a-6542-11e8-9707-cd5fe9e355c9.png)
  
5. De acuerdo a los requerimientos se siguen los mismos pasos para crear el segundo contenedor con el nombre webserver2.
    ```
    lxc launch ubuntu:x webserver2
    ```  
![lxc launch webserver2](https://user-images.githubusercontent.com/35766585/40880618-0b48a262-667a-11e8-84bb-73da0bdec48a.PNG)

  Ahora que el contenedor esta ejecutándose, se usa el comando lxc list para mostrar la información al respecto.
    ![comando7](https://user-images.githubusercontent.com/35766585/40827540-7cfd345a-6543-11e8-9ec0-5b30fe80b685.png)
  
6. Se Configura el servidor web en cada contenedor Nginx
    ```
    lxc exec webserver1-- sudo --login --user ubuntu
  
    lxc exec webserver2-- sudo --login --user ubuntu
    ```

7. Una vez dentro del contenedor, el shell prompt se vera de la siguiente manera:
  ![lxc exec webserver1](https://user-images.githubusercontent.com/35766585/40880631-2501dd7c-667a-11e8-971b-c08effa5afb7.png)
  ![lxc exec webserver2](https://user-images.githubusercontent.com/35766585/40880632-27de1f4c-667a-11e8-9743-4f38dec2d8de.png)
    
8. Para configurar Nginx en este contenedor, se actualiza el paquete dentro del contenedor y se instala nginx

    ![comando12](https://user-images.githubusercontent.com/35766585/40868214-7096dc9c-65d0-11e8-8642-fbee0f650518.png)

9. Se edita la página por defecto del servidor web WEBSERVER1 mediante el siguiente comando:

   ![comando13](https://user-images.githubusercontent.com/35766585/40829655-bbbc68c2-6549-11e8-8128-eb6c2e79d46a.png)
  
    Se hace el siguiente cambio en el archivo:      
     ![webserver1](https://user-images.githubusercontent.com/35766585/40880507-fe503ab8-6677-11e8-8207-1cddbea8bb1c.PNG)
    
    Se edita la pagina por defecto del servidor web WEBSERVER2 mediante el siguiente comando:
    ![comando15](https://user-images.githubusercontent.com/35766585/40830052-e5f7137a-654a-11e8-9bca-fd8fc5307a07.png)
  
    Se hace el siguiente cambio en el archivo:
     ![webserver2](https://user-images.githubusercontent.com/35766585/40880520-290f7994-6678-11e8-9c55-ab055b6783d3.PNG)
    
10. Para asignar un procesador único para cada servicio web, se ejecutan los siguientes comandos:
     ```
     $ lxc config set webserver1 limits.cpu 1  
     $ lxc config set webserver2 limits.cpu 1 
     ```
11. Se reinicia el servicio nginx y salimos del contenedor, usando el comando:
    ```
    sudo service nginx restart
    logout
    ```

**6.**
# CREACIÓN DE CONTENEDOR CON SERVICIO DE BALANCEO DE CARGA
1. Se crea el balanceador de carga
![comando1](https://user-images.githubusercontent.com/35766585/40830675-a9767948-654c-11e8-9ff7-b3b7ebfa7dc7.png)

2. Para configurar el balanceador de carga se ejecuta el siguiente comando
![comando2](https://user-images.githubusercontent.com/35766585/40830793-ec241fac-654c-11e8-9075-e1f31e3d303a.png)

3. Para configurar Nginx en este contenedor, se actualiza el paquete dentro del contenedor y se instala nginx
![comando3](https://user-images.githubusercontent.com/35766585/40831448-a4fe9f7e-654e-11e8-91cb-f1c7a080d210.png)

4. Se configura el balanceador de carga mediante el siguiente comando:
  ![comando4](https://user-images.githubusercontent.com/35766585/40831783-85743618-654f-11e8-8566-81fb2bd03be6.png)

   Se edita el archivo, para configurar el bloque de servidores a los que se les hará las peticiones  
  ![editloadbalancer](https://user-images.githubusercontent.com/35766585/40880745-0ddf317e-667c-11e8-9c73-8e84f93a31ec.PNG)

5. Se debe eliminar la página por default, para que cuando se hagan solicitudes al balanceador no retorne la pag html y reiniciar el servicio nginx

    ![comando6](https://user-images.githubusercontent.com/35766585/40832709-90fda5d0-6551-11e8-83c4-bfff49f6cc15.png)
    
 6. Se debe configurar el balanceador de carga para recibir conexiones remotas: Para esto se debe alterar la iptable, que es la que permite el intercambio de tráfico entre el http del host y el contenedor de balanceador de carga.
  ```
  sudo iptables -t nat -A PREROUTING -p tcp -m conntrack --ctstate NEW --dport 80 -j DNAT --to-destination 10.213.254.175:80  
  ```
  
7. Mediante el comando systemctl se valida que el servicio queda activo
  ```
  systemctl list-unit-files --state=enabled | grep lxd
  ```

**7.**
# SALIDA DEL COMANDO LXC LIST CON LOS CONTENEDORES CREADOS Y SUS DIRECCIONES IP 

  ![captura](https://user-images.githubusercontent.com/35766585/40833024-564a3eac-6552-11e8-9730-3147f0a0df81.PNG)

**8.**
# PRUEBAS DEL FUNCIONAMIENTO DEL BALANCEADOR
1. A continuación se muestra por medio del uso del comando curl la respuesta de cada uno de los servicios web a través del balanceador.
  
  ```
  $ curl http:// 10.213.254.210.175
  ```
  ![curl 10213254175webserver1](https://user-images.githubusercontent.com/35766585/40833358-59f3fb00-6553-11e8-9e55-1fce96daf4fa.PNG)
    
  ![curl 10213254175webserver2](https://user-images.githubusercontent.com/35766585/40833425-84a28150-6553-11e8-9137-d16c2510bb60.PNG)
  
2. Por medio de la herramienta de stress SIEGE se evalue el desempeño del sistema con las siguientes configuraciones:
  * Servidores web con 64Mb, 128Mb
  * Servidores web con 50%CPU, 100%CPU
  
  Para llevar a cabo estas pruebas debo se realizan los siguientes pasos:
  * Instalar Siege
  ```
  sudo apt-get install siege  
   ```  
  * Se configura la CPU y la memoria RAM de los contenedores mediante los siguientes comandos:
  
  CPU 
  ```
  lxc config set webserver# limits.cpu.allowance 100%
  lxc config set webserver# limits.cpu.allowance 50%
  ```

  Memoria RAM
  ```
  lxc config set webserver# limits.memory 64MB
  lxc config set webserver# limits.memory 128MB
  ```
  
  * Se ejecuta el siguiente comando, para cada combinacion:
  ```
  siege -c 100 -t 30s http://10.213.254.175 
  ```
  De acuerdo a lo anterior se obtuvieron los siguientes resultados:
    
  **CPU 100% MEMORIA 64MB - WEBSERVER1**  
  ![capturacpu100memory64webserver1](https://user-images.githubusercontent.com/35766585/40869386-001dd1c2-65e0-11e8-8088-5427f64e158f.PNG)
  
  **CPU 100% MEMORIA 128MB - WEBSERVER1**
  ![capturacpu100memory128webserver1](https://user-images.githubusercontent.com/35766585/40869388-0366ce92-65e0-11e8-9c52-7e4a10721b09.PNG)

  **CPU 50% MEMORIA 64MB - WEBSERVER1**
  ![capturacpu50memory64webserver1](https://user-images.githubusercontent.com/35766585/40869389-066b90b4-65e0-11e8-98c6-40bb615e306f.PNG)

  **CPU 50% MEMORIA 128MB - WEBSERVER1**
  ![capturacpu50memory128webserver1](https://user-images.githubusercontent.com/35766585/40869392-0954e2f8-65e0-11e8-8a1f-100e26d8f430.PNG)

**CPU 100% MEMORIA 64MB - WEBSERVER2**
![capturacpu100memory64webserver2](https://user-images.githubusercontent.com/35766585/40869432-7f911b44-65e0-11e8-976a-130cfdadb57b.PNG)  
  **CPU 100% MEMORIA 128MB - WEBSERVER2**
  ![capturacpu100memory128webserver2](https://user-images.githubusercontent.com/35766585/40869443-afd8a4ca-65e0-11e8-8a88-dff970fcb25b.PNG)
  
  **CPU 50% MEMORIA 64MB - WEBSERVER2**
  ![capturacpu50memory64webserver2](https://user-images.githubusercontent.com/35766585/40869452-e0b5467a-65e0-11e8-8019-482db79c1f6c.PNG)
  
  **CPU 50% MEMORIA 128MB - WEBSERVER2**
  ![capturacpu50memory128webserver2](https://user-images.githubusercontent.com/35766585/40869453-ec49d4a6-65e0-11e8-8377-c91e3c56886a.PNG)

**9.**
# CONFIGURACIÓN DEL REENVIO DE PUERTOS EN LA MÁQUINA VIRTUAL PARA PERMITIR EL ACCESO DESDE EL SISTEMA ANFITRIÓN HACIA EL CONTENEDOR CON EL SERVICIO PARA BALANCEO DE CARGA

  La última pieza del rompecabezas es conectar el contenedor del servidor web a Internet. Nginx está instalado en un contenedor y, de     forma predeterminada, no es accesible desde Internet. Es necesario configurar el servidor para reenviar cualquier conexión que pueda     recibir de internet en el puerto 80 al contenedor del servidor web. Para hacer esto, se crea una regla de iptables para reenviar las     conexiones.
  Para permitir las peticiones al balanceador de carga desde el SO anfitrión se debe crear una regla de FORWARDING en el SO Ubuntu, por   medio del siguiente comando:
  1.  Declarar la dirección ip pública y la del contenedor que tiene el balanceador de carga, especificando el puerto.
  ```
  PORT=80 PUBLIC_IP=172.30.175.29 CONTAINER_IP=10.213.254.175 

   ```  
   2. El comando iptables requiere dos direcciones IP: La IP publica del servidor y la IP privada del contenedor nginx  
  ```
  sudo iptables -t nat -I PREROUTING -i enp0s8 -p TCP -d $PUBLIC_IP --dport $PORT -j DNAT --to-destination $CONTAINER_IP:$PORT -m     
  comment --comment "forward to the Nginx container"
  ```
  3. Para comprobar el acceso desde el sistema anfitrión hacia el contenedor, se inicia el navegador y se ingresa la dirección IP   pública para hacer las peticiones, obteniendo el siguiente resultado:  
  ![captura3](https://user-images.githubusercontent.com/35766585/40869971-bd9c4e18-65ea-11e8-94c7-4fa15608face.PNG)
  
  Si se actualiza la página del navegador se obtiene lo siguiente:
  ![captura2](https://user-images.githubusercontent.com/35766585/40869978-df3cef28-65ea-11e8-8213-465b1a9dd9af.PNG)
  
  
  # OPCIONAL
  1. Configure un adaptador nictype: macvlan, de tal modo que los contenedores reciban una ip de la red local                            
  **R/**  
  Una Macvlan permite que una sola interfaz física tenga múltiples direcciones mac e IP utilizando las subinterfaces Macvlan. Esto es     diferente de crear una subinterfaz en una interfaz física usando vlan. Con las subinterfaces vlan, cada subinterfaz pertenece a un       dominio L2 diferente que utiliza vlan y todas las subinterfaces tienen la misma dirección MAC. Con macvlan, cada subinterfaz obtendrá   una dirección MAC y una dirección IP exclusivas y se expondrá directamente en la red subyacente. La interfaz de Macvlan se usa           generalmente para aplicaciones de virtualización y cada interfaz de macvlan está conectada a un contenedor o máquina virtual. 
  Cada contenedor o VM puede obtener directamente la dirección dhcp de un servidor común como lo haría el host. 
  
  ![vlan2](https://user-images.githubusercontent.com/35766585/40881441-707f58f8-668c-11e8-85d9-137284dff626.png)
  
  A continuación se mostrara la creación de dos macvlans:
  
  ![mvlan](https://user-images.githubusercontent.com/35766585/40881479-ff8cf8a2-668c-11e8-9bf5-c924d216de62.PNG)  
  
  # PREGUNTA RANDOM
  2. Al reiniciar la máquina virtual en que estado quedan los contenedores?                                                              
  **R/**
  Al reiniciar la máquina virtual los contenedores quedan en el estado que se encuentren en el momento del reinicio.
  Por Ejemplo: 
  Para apagar el contenedor webserver1 se utiliza el siguiente comando:
  ```
  lxc stop ubuntu:x webserver1
  ```
  El resultado obtenido es el siguiente:
  
  ![stopwebserver1](https://user-images.githubusercontent.com/35766585/40880476-4d8cba94-6677-11e8-90f2-f9ae9c9b6e02.png)
  
  Al reiniciar la máquina virtual los contenedores permanecen en el estado que quedaron.
  
  # BIBLIOGRAFIA
  
  - REPOSITORIO GITHUB                                                                                                                   
    URL: https://github.com/ICESI                                                                                                       
    URL: https://github.com/ICESI-Training
  
  - Ubuntu 16.04.4 LTS (Xenial Xerus)
  
    URL: http://releases.ubuntu.com/16.04/
  
  - How to Set Up and Use LXD on Ubuntu 16.04
  
    URL: https://www.digitalocean.com/community/tutorials/how-to-set-up-and-use-lxd-on-ubuntu-16-04
    
 -  Contenedores con LXD/LXC
 
    URL: https://elpuig.xeill.net/Members/vcarceler/articulos/contenedores-con-lxd-lxc
   
 -  Setting CPU Resource Limits With LXC
 
    URL: https://www.jamescoyle.net/how-to/2532-setting-cpu-resource-limits-with-lxc
  
 -  Macvlan and IPvlan basics
 
    URL: https://sreeninet.wordpress.com/2016/05/29/macvlan-and-ipvlan/
    
 
  
  
  

