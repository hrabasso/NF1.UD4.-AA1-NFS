# NF1.UD4.-AA1-NFSa

Per començar, hem de crear 2 maquines virtuals, en aquesta practica hem de crear una maquina **ubuntu server** i una **zorin** per simular el **servidor i el client**.
Les dues maquines han d'estar en: la primera en **NAT** i la segona en **HOST ONLY**.

![Captura 1](img/1.png)
![Captura 2](img/2.png)

---
Un cop que ja tenim les dues maquines configurades començem configuran el servidor (ubuntu base)

La primera comanda que utilitzarem servira per actualitzar els paquets.

```bash
sudo apt update && sudo apt upgrade -y 
```
![Captura 3](img/3.png)

Cuan hem executat la comanda, hem de tornar a executarla per comprobar que no quedin actualitzacions.

---
Un cop que ja tenim actualitzat tots els paquets i ho haguem comprobat, el seguent pas sera començar amb la creació de l'estructura de carpetas, de grups i usuaris.

El primer que farem sera crear els grups neccesaris, en aquest cas en demana que creem 2 grups, el primer **devs** i el segon **admin**

Per crear aquest grups farem la seguent comanda 

```bash
groupadd devs
```
I tambe la comanda:

```bash
groupadd admin
```
![Captura 4](img/4.png)

---
Per poder comprovar que els grups s'han creat correctament farem servir la comanda greep dev per buscar els 2 grups, el grup **devs** i el grup **admin**, hem d'entrar dins de l'arxiu /etc/groups, per fer-ho farem la seguent comanda:

```bash
grep devs /etc/group
```
I tambe la comanda:

```bash
grep admin /etc/group
```

En la qual com podrem veure en la captura, els 2 grups d'han creat correctament

![Captura 5](img/5.png)

---
Un cop que ja tenim els grups creats el seguent pas sera crear l'usuari **dev01** que formi part del grup **devs**, per fer això farem servir la seguent comanda

```bash
useradd -G devs -m -s /bin/bash dev01
```

Per confirmar que esta creat l'usuari dev01 correctament fem servir el grep dev un alre cop pero ara hauriem de fer dev01 per localitzar especialment aquell usuari.

```bash
grep dev01 /etc/passwd
```

![Captura 6](img/6.png)

---
Un cop que ja tenim l'usuari dev01 el seguent pas sera crear el segon usuari que en aquest cas seria **admin01**, ha de formar part del grup **admin**, per fer això farem servir la seguent comanda

```bash
useradd -G admin -m -s /bin/bash admin01
```

Per confirmar que esta creat l'usuari **admin01** correctament fem servir la comanda grep com anteriorment pero hem de posar grep admin01 per localitzar el usuari admin i no dev.

```bash
grep admin01 /etc/passwd
```
![Captura 7](img/7.png)

---
Un cop que ja hem creat els grups i els usuaris, el seguent pas es crear el directori per als projectes en la qual la ruta que ens demana és la seguent /srv/nfs/dev_projects, per crear les totes les carpetes sense haber de fer mes d'una comanda hauriem de possar mkdir per crear carpetes i -p per ferles totes.
```bash
mkdir /srv/nfs/dev_projects -p
```
![Captura 8](img/8.png)

---
Un cop fet això crearem el directori per a les eines d'administració en la qual la ruta sera /srv/nfs/admin_tools.

```bash
mkdir /srv/nfs/admin_tools
```

![Captura 9](img/9.png)

---
Per ultim hem de configurar els permisos de les carpetes, en aquest cas utilitzare la comanda Chown per canviar les propietats de la carpeta.

```bash
chown root:devs /srv/nfs/dev_projects
```

```bash
chown root:admin /srv/nfs/admin_tools
```
![Captura 10](img/10.png)

Un cop possat les propietats, hem d'assignar els permisos a la carpeta (dev_projects) amb la comanda chmod 770

```bash
chmod 770 /srv/nfs/dev_projects
```

```bash
chmod 770 /srv/nfs/admin_tools
```

![Captura 11](img/11.png)

Per comprobar que els permisos s'han aplicat correctament hem de utilitzar la següent comanda per veure els permisos.

![Captura 12](img/12.png)

---
## Fase 2

Per poder continuar hem de crear els grups i usuaris dins de la maquina client (Zorin), per crear els grups i usuaris farem instalem la aplicació "users and groups". Hem de seguir els següents pasos tal com ho indico a les captures.

![Captura 13](img/13.png)

![Captura 14](img/14.png)

![Captura 15](img/15.png)

![Captura 16](img/16.png)

![Captura 17](img/17.png)

Hem de comprobar que els números d'identificació coincideixin a les dues màquines tal com surt en la captura.

![Captura 18](img/18.png)
Els numeros que surten hem de mirar si son els mateixos que ens dona l'altre maquina virtual i si son els mateixos podem continuar, sino has de tornar a crear els grups.

---
## Instal·lació i configuració del servei NFS

Ara instal·larem el servidor NFS amb la comanda: sudo apt install (i el nom de el servidor que volem instalar)

```bash
sudo apt install nfs-kernel-server -y
```

![Captura 19](img/19.png)

Veiem que s'ha instal·lat correctament i podem continuar, sino hem de tornar a fer la comanda i si surt error hauriem de comprobar si tenim wifi.

![Captura 20](img/20.png)

---
En aquest pas hem de configurar l'exportació dels directoris amb les opcions corresponents.

Per a fer-ho haurem de editar l'arxiu `/etc/exports`. Pero per poder editar-ho correctament hem d'utilitzar sudo nano, sino no deixara editar el archiu correctament.

```bash
sudo nano /etc/exports
```

![Captura 21](img/21.png)

---
El que necesitem editar simplement seria afegir aquesta comanda a sota de tot com surt en la següent captura.

```bash
/srv/nfs *(rw,sync,no_subtree_check)
```

![Captura 22](img/22.png)

---
Ara hem d'aplicar el canvis, ho haurem de fer reinciant el servei amb la comanda sudo system restart (i el nom del servidor kernel). La comanda seria la següent:

```bash
sudo systemctl restart nfs-kernel-server
```
Cuan acabem reiniciant el servei hem d'iniciarlo i comprobar que tot esta correcta i funciona.

```bash
exportfs -u
```
Amb aquesta comanda podem veure quins arxius es poden exportar. La captura et mostra que hauria de sortir.

![Captura 23](img/23.png)

---
Tambe podem fer la seguent comanda per veure en quin port treballa, en aquest cas ho fa amb el port 2049 que es el que inidica **NFS**.

```bash
rpcinfo -p 192.168.56.103
```
(Hem de possar la ip de la maquina que seria basicament la del servidor)

![Captura 24](img/24.png)

---
Per poder comprobar si hem fet be el anterior pas hem d'instalar el paquet nfs-common amb sudo apt install.
Ho hem de fer desde el cmd del client. Si ho fas desde el servidor no ho podrem comprobar correctament.

```bash
sudo apt install nfs-common -y
```

---
Cuan hem acabat de instalar nfs-common ens hem de conectar al servidor, ho hem de fer amb la comanda showmount -e (192.168.56.103) aquesta seria la meva comanda ja ques es la meva ip pero hauries de fer amb la teva ip.

```bash
showmount -e 192.168.56.103
```

![Captura 25](img/25.png)

---

# Fase 3

A hem de fer una prova.

Previament ja hem fet tots els pasos anterios hem exportat l'arxiu /srv/nfs, per tant el seguent pas que hem de fer sera montar el recurs a la carpeta /mnt/admin_tools, aquesta carpeta no existeix, per tant el primer pas es crear-la, ho farem amb la seguent comanda mkdir que s'utlitza per crear carpetas com he mencionat anteriorment i possar la ruta de la carpeta.

```bash
mkdir /mnt/admin_tools 
```

![Captura 26](img/26.png)

---
Cuan ja la tenim creada la carpeta /mnt/admin_tools, el seguent pas es montar el recurso, això ho hem de fer amb la comanda mount -t.

```bash
mount -t nfs 192.168.56.101:/srv/nfs/admin_tools /mnt/admin_tools
```

Com podem veure a la captura no podem crear cap arxiu ja que no tenim els pemisos ja que el root de la maquina client i el root del servidor no seria el mateix. Si et deixa crear el archiu comproba anteriorment si has aplicat be els permisos.

![Captura 27](img/27.png)

---
Si intentem crear un arxiu amb l'usuari admin si que podrem, ja que aquest usuari si que te permisos en aquesta carpeta, si no et deixa et recomano que tornis a veure els permisos.

![Captura 28](img/28.png)

---
Podriem veure que l'arxiu que hem creat es de la propietat de admin01 com es veu a la captura.

![Captura 29](img/29.png)

A continuació farem la segona prova que consisteix en poder crear arxius amb root.

---
Prova 2
---
Per començar hem d'editar l'arxiu /etc/exports en el qual ja habiem possat una linea, la hem de esborrar i sustituir per les següents dos lineas.

```bash
/srv/nfs/admin_tools *(rw,sync,no_subtree_check,no_root_squash)
/srv/nfs/dev_projects *(rw,sync,no_subtree_check)
```

---
Un cop possat les dos lineas i sustituit per la linea anterior hem de reiniciar el servei per poder aplicar els canvis com hem fet anteriorment. 

```bash
systemctl restart nfs-kernel-server
```

---
Ara haurem de desmontar i montar un altre cop el recurs, la meva comanda seria aquesta: 

![Captura 30](img/30.png)

---
Un cop montat i desmontat el recurs ja podem crear un nou arxiu, per exemple en aquest cas he creat una arxiu anomenat file2 ja que el file1 ja esta creat.

![Captura 32](img/32.png)

![Captura 31](img/31.png)

Si et deixa es perque hem modificat l'arxiu /etc/exports fent que el root de la maquina fisica sigui el mateix que el root del servidor, per tant tenim total llibertat per modificar l'arxiu.

---

# Fase 4

A continuació el client ens posa un altre prova, la prova seria que la xarxa d'administració (192.168.56.0/24) hi pugui escriure i editar-ho, però la xarxa de consultors (192.168.56.128) només pugui llegir.

---
Per poder fer això haurem de modificar l'arxiu /etc/exports i substituir la linia "/srv/nfs/dev_projects *(rw,sync,no_subtree_check)" per les seguents:

```bash
/srv/nfs/dev_projects 192.168.56.0/24(rw,sync,no_subtree_check)
/srv/nfs/dev_projects 192.168.56.128(ro,sync,no_subtree_check)
```
![Captura 33](img/33.png)

Aquestes lineas les possem per poder assignar permisos, si tenen una ip podran editar-ho, si tenen la altre no podran. Depen de la ip que tingui l'usuari.

---
Un cop fet, reinciem el servei.

```bash
systemctl restart nfs-kernel-server
```

---
Ara hem de montar el disc dev_projects per comprobar que tot funciona correctament. Primer hem de crear la carpeta.

```bash
mkdir /mnt/dev_projects
```

---
El seguent pas que farem sera modificar la nostre ip, en aquest cas probarem la ip ```192.168.56.199``` per poder fer això anirem a la configuració de xarxa i colocarem la ip manualment i montarem el disc

![Captura 34](img/34.png)

---

Per ultim hem de fer login amb l'usuari dev01 i probem a crear un arxiu en la carpeta dev_projects

![Captura 35](img/35.png)

Podem veure que no podem crear cap arxiu dins de la carpeta dev_projects perque no tenim els permisos neccesaris ja que l'usuari dev01 no forma part del grup admin.

---
# Fase 5
---
**Ara per ultim modificarem l'arxiu /etc/fstab per poder configurar els recursos compartits, per a que no es tinguin que montar cada vegada que entrem.**
---
Per començar hem d'entrar al arxiu.

```bash
sudo nano /etc/fstab
```

Ara hauriem d'afegir aquestes dues lines al final de la mateixa manera que es veu a la caprtura.

```bash
192.168.56.103:/srv/nfs/admin_tools /mnt/admin_tools nfs defaults 0 0
192.168.56.103:/srv/nfs/dev_projects /mnt/dev_projects nfs defaults 0 0
```
![Captura 36](img/36.png)

---
Un cop fet això hem de reiniciar la maquina i confirmem si els discos s'han montat correctament. 

![Captura 37](img/37.png)

---

# Conclusió

En general, la creació del servidor NFS és una eina potent i útil, però la manera com l'hem utilitzat presenta algunes limitacions importants. Un dels principals inconvenients és haver de crear els usuaris i grups tant al servidor com a cada màquina client, això fa repetir els mateixos passos moltes vegades i generar una càrrega de feina innecessària.

Per evitar aquest problema i millorar l’eficiència del sistema, seria molt més adequat centralitzar l’autenticació i la gestió d’usuaris. Una solució realista seria implementar un servidor LDAP, com OpenLDAP o una alternativa similar, que permeti administrar comptes, grups i paràmetres específics per a cada departament des d’un únic punt. Això no només simplificaria el manteniment, sinó que també incrementaria la seguretat i faria el desplegament molt més escalable de cara al futur. Pero en conclusió a sigut una tasca bastant llarga perque hem hagut de repetir pasos bastants cops. Tema usuaris i grups per exemple.

- [Tornar al enunciat](readme.md)
