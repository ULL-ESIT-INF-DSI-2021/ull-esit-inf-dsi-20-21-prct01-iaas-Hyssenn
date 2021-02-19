# Práctica 1 - Configuración de Máquina virtual en el Iaas

# Introducción

Antes de comenzar con la realización de la práctica hemos tenido que realizar todas las tareas previas.

En esta práctica se aprenderá a configurar una máquina virtual de la plataforma universitaria IAAS en sus aspectos básicos, de conexión remota e instalación de git y Node.js

# Objetivos

Al finalizar dicha práctica tendremos:

- Una máquina virtual con un nombre predeterminado del pool con los aspectos básicos ya configurado.
- Conexión rémota por ssh ya configurada.
- Instalado Git y node.js en su última versión.

## Conexión a la máquina virtual y primera configuración

Para realizar la conexión a la maquina virtual de la plataforma he tenido que conectarme remótamente desde la vpn de la ULL siguiendo los pasos de este [enlace](https://www.ull.es/servicios/stic/2020/12/01/servicio-de-vpn-de-la-ull/)

Una vez en la vpn encendemos la máquina de la plataforma que nos asigna un número

![foto](https://github.com/ULL-ESIT-INF-DSI-2021/ull-esit-inf-dsi-20-21-prct01-iaas-Hyssenn/blob/main/docs/imagenes/Asignacion%20maquina.PNG?raw=true)


y procedemos a conectarnos remótamente por SSH con la línea: 

```
ssh usuario@10.6.130.6
```

Nos obligará a cambiar la contraseña la primera vez que nos conectemos y seguimos los pasos que se nos dice

![foto](https://github.com/ULL-ESIT-INF-DSI-2021/ull-esit-inf-dsi-20-21-prct01-iaas-Hyssenn/blob/main/docs/imagenes/Cambiar%20contra.png?raw=true)

Lo siguiente que realizamos son los cambios al nombre de host de la máquina virtual en /etc/hostname y en /etc/hosts

![foto](https://github.com/ULL-ESIT-INF-DSI-2021/ull-esit-inf-dsi-20-21-prct01-iaas-Hyssenn/blob/main/docs/imagenes/Cambio%20nombres.png?raw=true)

y reiniciamos la máquina

```
usuario@ubuntu:~$ sudo apt update
...
usuario@ubuntu:~$ sudo apt upgrade
...
```

### Configuración del SSH

Para evitar recordar la ip y tener que usarla en cada conexión SSH incluimos la información de la maquina en etc/hosts.

![foto](https://github.com/ULL-ESIT-INF-DSI-2021/ull-esit-inf-dsi-20-21-prct01-iaas-Hyssenn/blob/main/docs/imagenes/FicherosHostLocal.png?raw=true)

generamos el par de claves pública-privada.

![foto](https://github.com/ULL-ESIT-INF-DSI-2021/ull-esit-inf-dsi-20-21-prct01-iaas-Hyssenn/blob/main/docs/imagenes/Generaci%C3%B3nClave.png?raw=true)

y copiamos la clave pública de nuestra maquina local  a la virtual

![foto](https://github.com/ULL-ESIT-INF-DSI-2021/ull-esit-inf-dsi-20-21-prct01-iaas-Hyssenn/blob/main/docs/imagenes/Copia_inicio_ssh.png?raw=true)

![foto](https://github.com/ULL-ESIT-INF-DSI-2021/ull-esit-inf-dsi-20-21-prct01-iaas-Hyssenn/blob/main/docs/imagenes/confirmacion_de_inicio.png?raw=true)

y ya se puede hacer login sin necesidad de ninguna contraseña o ip simplemente desde el nombre.
El siguiente paso fue generar de la misma manera un par de claves en la máquina con el siguiente código
```
usuario@iaas-dsi58:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/usuario/.ssh/id_rsa): /home/usuario/.ssh/id_rsa
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/usuario/.ssh/id_rsa.
Your public key has been saved in /home/usuario/.ssh/id_rsa.pub.
The key fingerprint is:
...
```
y por último procedemos a la instalación de git y node.js

## Instalación de git y Node.js en la máquina virtual del Iaas

Instalación de git

```
usuario@iaas-dsi58:~$ sudo apt install git
[sudo] contraseña para usuario: 
Leyendo lista de paquetes... Hecho
Creando árbol de dependencias       
Leyendo la información de estado... Hecho
git ya está en su versión más reciente (1:2.17.1-1ubuntu0.7).
Los paquetes indicados a continuación se instalaron de forma automática y ya no son necesarios.
  linux-headers-4.15.0-117 linux-headers-4.15.0-117-generic
  linux-image-4.15.0-117-generic linux-modules-4.15.0-117-generic
  linux-modules-extra-4.15.0-117-generic
Utilice «sudo apt autoremove» para eliminarlos.
0 actualizados, 0 nuevos se instalarán, 0 para eliminar y 0 no actualizados.
```

Configuramos git con nuestra información básica

```
usuario@iaas-dsi58:~$ git config --global user.name "Enrique Hernandez Cabrera"
usuario@iaas-dsi58:~$ git config --global user.email alu0101033266@ull.edu.es
usuario@iaas-dsi58:~$ git config --list
user.name=Enrique Hernandez Cabrera
user.email=alu0101033266@ull.edu.es
usuario@iaas-dsi58:~$ 
```
Y configuramos el prompt de la terminal para que aparezca la rama actual en la que nos encontramos cuando accedemos a algún directorio que resulta
estar asociado a un respositorio git. Creamos primero el git-prompt.sh y copiamos toda el código de este [enlace](https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh)


```
usuario@iaas-dsi58:~$ vi git-prompt.sh
usuario@iaas-dsi58:~$ mv git-prompt.sh .git-prompt.sh
usuario@iaas-dsi58:~$ vi .bashrc
usuario@iaas-dsi58:~$ tail .bashrc
if ! shopt -oq posix; then
  if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi
source ~/.git-prompt.sh
PS1='\[\033]0;\u@\h:\w\007\]\[\033[0;34m\][\[\033[0;31m\]\w\[\033[0;32m\]($(git branch 2>/dev/null | sed -n "s/\* \(.*\)/\1/p"))\[\033[0;34m\]]$'
(solo copiar las ultimas dos lineas del tail)

usuario@iaas-dsi58:~$ exec bash -l
[~()]$
```
Luego copiamos nuestra clave pública y la añadimos al github en la sección de la cuenta donde pone add SSH key todo para clonar luego el repositorio

![foto](https://github.com/ULL-ESIT-INF-DSI-2021/ull-esit-inf-dsi-20-21-prct01-iaas-Hyssenn/blob/main/docs/imagenes/SSH_Key_Github.png?raw=true)

copiamos el repositorio
```
[~()]$git clone git@github.com:ULL-ESIT-INF-DSI-2021/prct01-iaas-vscode.git
Clonando en 'prct01-iaas-vscode'...
The authenticity of host 'github.com (140.82.121.3)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,140.82.121.3' (RSA) to the list of known hosts.
remote: Enumerating objects: 156, done.
remote: Counting objects: 100% (156/156), done.
remote: Compressing objects: 100% (117/117), done.
remote: Total 156 (delta 67), reused 85 (delta 34), pack-reused 0
Recibiendo objetos: 100% (156/156), 351.19 KiB | 1.31 MiB/s, listo.
Resolviendo deltas: 100% (67/67), listo.
[~()]$ls
prct01-iaas-vscode
[~()]$cd prct01-iaas-vscode/
[~/prct01-iaas-vscode(main)]$
```
Para finalizar instalamos nvm y Node.js en su última versión

![foto](https://github.com/ULL-ESIT-INF-DSI-2021/ull-esit-inf-dsi-20-21-prct01-iaas-Hyssenn/blob/main/docs/imagenes/Instalacion_nvm.png?raw=true)

![foto](https://github.com/ULL-ESIT-INF-DSI-2021/ull-esit-inf-dsi-20-21-prct01-iaas-Hyssenn/blob/main/docs/imagenes/Install_node.png?raw=true)


