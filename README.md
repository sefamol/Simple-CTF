Simple CTF, es un desafio de THM que se puede resolver utilizando inyección sql, para posteriomente realizar el escalamiento de privilegios utilizando el editor de texto Vim.

# Enumeración

**Pregunta 1: Cuantos servicios se encuentran por debajo del puerto 1000?**
*How many services are running under port 1000?*

`nmap -sV -sC ip_objetivo`

![Pasted image 20230304204458](https://user-images.githubusercontent.com/24280145/222960870-d75c224c-b075-46de-99d8-5a6e60dfd363.png)

**Respuesta**: 2

**Pregunta 2: Que servicio esta corriendo en el puerto más alto?**
*What is running on the higher port?*

**Respuesta**: ssh

**Pregunta 3: Cual es el CVE que se utiliza contra la aplicación?**
*What's the CVE you're using against the application?*

Realizamos la enumeración de las vulnerabilidades de los servicios de la web sin resultados

`nmap -sV --script vuln ip_objetivo`

![Pasted image 20230304204614](https://user-images.githubusercontent.com/24280145/222960917-abd13058-ec59-4430-b9ae-b8cef15ed637.png)

![Pasted image 20230304204656](https://user-images.githubusercontent.com/24280145/222960920-3d825da6-d61a-4b6d-bf75-08c353595e06.png)

## gobuster

Realizamos la enumeración de los directorios, hallando 1 directorio

`gobuster dir -u http://ip_objetivo -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 15`

![Pasted image 20230304204901](https://user-images.githubusercontent.com/24280145/222960958-fc8b2435-29dc-4a37-9b12-b7d7f004a5ff.png)

Revisamos el directorio `/simple`

usamos la url `http://ip_objetivo/simple` en el navegador

## Wappalizer
Revisamos las tecnologias presentes en la web

![Pasted image 20230304205112](https://user-images.githubusercontent.com/24280145/222961048-b0f8a52f-ba9a-4fbb-9218-6386995fabf5.png)

Revisando la información de apache (un posible CVE) no encontramos nada

solo queda el cms, buscamos la versión

![Pasted image 20230304205329](https://user-images.githubusercontent.com/24280145/222961195-a16fdfce-d41b-4d42-80b8-fee3776bec67.png)

buscamos suerte con la versión

**Respuesta**: CVE-2019-9053

## Obtención de credenciales

**Pregunta 4: Que tipo de vulnerabilidad tiene la aplicación?**
*To what kind of vulnerability is the application vulnerable?*

Buscamos en los detalles del CVE
https://nvd.nist.gov/vuln/detail/CVE-2019-9053

**Respuesta**: SQLi

**Pregunta 5: Cual es el password?**
*What's the password?*

Buscamos la vulnerabilidad en google, hallamos un script 

https://github.com/e-renna/CVE-2019-9053

fijamos la wordlist y corremos el script

`python exploit.py -u http://ip_objetivo/simple --crack -w /usr/share/wordlists/SecLists/Passwords/Common-Credentials/best110.txt`

**Nota**: *Corrí el script en vscode en entorno virtual, recibí muchos errores hasta q termino correctamente el proceso, Se debe ser paciente*

![Pasted image 20230304222502](https://user-images.githubusercontent.com/24280145/222961336-b9dc6096-2670-4ef5-b2f9-e2ad25568056.png)

*Respuesta**: secret
**Nota**: +Existe un error en el usuario, mitchs no permite la conexión, el usuario es mitch*

**Pregunta 6: Donde puedo logear con el acceso obtenido?**
*Where can you login with the details obtained?*

**Respuesta**: ssh

`ssh mitch@ip_objetivo -p2222`

**Pregunta 7: Cual es la flag del usuario**
*What's the user flag?*

![Pasted image 20230304224645](https://user-images.githubusercontent.com/24280145/222962280-f258f3cb-5d9a-4fe4-93fe-0ab9a5a8758d.png)

**Respuesta**: G00d j0b, keep up!

**Pregunta 8: Existe otro usuario además de mitch?**

ejecutamos pwd para conocer nuestra ubicación

![Pasted image 20230304225611](https://user-images.githubusercontent.com/24280145/222962332-3963a7b7-30dc-4ca6-87fe-c8628f4e5661.png)


![Pasted image 20230304225303](https://user-images.githubusercontent.com/24280145/222962336-b035d25f-cf43-4ac3-b86e-06581c9d8a99.png)

**Respuesta**: sunbath

## Elevación de privilegios

**Pregunta 9: Podemos elevar los privilegios utilizando una shell?**
*What can you leverage to spawn a privileged shell?*

Verificamos los permisos que tenemos como usuario

![Pasted image 20230304225828](https://user-images.githubusercontent.com/24280145/222962368-27baa987-705a-4c7e-b4b6-cbc27bf64f70.png)

**Respuesta**: vim

**Pregunta 10: Cual es la flag de root?**

Ejecutamos el comando !sh en vim

![Pasted image 20230304232441](https://user-images.githubusercontent.com/24280145/222962404-335cfcd3-4b68-4155-bcd7-004432e3f93b.png)

*Teoría sobre la ejecución en vim*: https://www.youtube.com/watch?v=X60hEP3msEQ

![Pasted image 20230304232109](https://user-images.githubusercontent.com/24280145/222962449-fde215c6-7865-46ee-af66-ea080ecefd65.png)

![Pasted image 20230304232226](https://user-images.githubusercontent.com/24280145/222962455-8cdef72c-c230-4c80-bfdf-47c9f0afed0a.png)

**Respuesta**: W3ll d0n3. You made it!
