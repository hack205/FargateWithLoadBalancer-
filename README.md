# FargateWithLoadBalancer-

FargateWithLoadBalancer 

Pondremos un sistema en producción utilizando la tecnología de Fargate y agregándole un certificado https junto con un balanceador. 

Primero necesitamos tener una imagen en nuestro repositorio, Fargate nos permite trabajar con dos tipos de repositorios:

1- Amazon ECR

2- Docker Hub

En esta ocasión trabajaremos con el repositorio de Amazon ECR, Nosotros ya tenemos nuestra imagen en nuestro repositorio:


![image](https://user-images.githubusercontent.com/36380066/131719720-91890f5f-5568-4b67-bda2-1bb3dc0dce74.png)


Como primer paso crearemos una task en Elastic Container Registry, la cual contendra la imagen que previamente mostre.


![image](https://user-images.githubusercontent.com/36380066/131720169-5eb67db6-e456-41f8-8de8-88256b44aff8.png)


![image](https://user-images.githubusercontent.com/36380066/131721374-158b9d28-8105-4219-9e4e-c1d56ff12337.png)


![image](https://user-images.githubusercontent.com/36380066/131722134-61781272-dc45-46a2-9b6d-f4ac41431e9a.png)

En el campo Image* pegamos el campo de URI en nuestro repositorio "COPY URI"

![image](https://user-images.githubusercontent.com/36380066/131721971-327693a1-915d-4017-ab9a-a28791a635c1.png)

En el campo Memory Limits (MiB) agregamos un valor de  2048, con esto definimos el limite de memoria rígido y / o flexible en MiB para mi contenedor

ademas agregamos los puertos, con los cuales trabajará nuestro sistema, en este caso abrimos el 80 y 443 para trabajar con el certificado https.

![image](https://user-images.githubusercontent.com/36380066/131722764-edce7925-56a1-47b5-ad79-3a4930037286.png)


![image](https://user-images.githubusercontent.com/36380066/131724143-601686ab-ef87-49cb-852d-9642a63dc825.png)

y creamos nuesto task.

![image](https://user-images.githubusercontent.com/36380066/131724483-608c5f15-6337-4a63-bf7c-0547b24ec64e.png)

Como segundo paso, crearemos nuestro balanceador de carga, este se encuentra en  EC2:

![image](https://user-images.githubusercontent.com/36380066/131725482-ea740068-d19e-483d-b23e-0542806ea971.png)

![image](https://user-images.githubusercontent.com/36380066/131725661-3fdc7ce9-4deb-469e-a13e-04d751560ffc.png)

![image](https://user-images.githubusercontent.com/36380066/131725980-a6392e96-32d9-400e-bdf8-ec60124ea774.png)

![image](https://user-images.githubusercontent.com/36380066/131726030-d7914dd5-2001-4cf2-84f3-50847a9c69a9.png)

Seleccionamos las zonas en las cuales estará disponible nuestro balanceador 

![image](https://user-images.githubusercontent.com/36380066/131726712-a6b46f01-95f0-4c1a-958f-812b23b42b25.png)

![image](https://user-images.githubusercontent.com/36380066/131727010-693974b5-ca4c-40ed-a830-b14cdcf80c31.png)

![image](https://user-images.githubusercontent.com/36380066/131727155-04053653-da49-4be5-b11d-eb445a545d08.png)

![image](https://user-images.githubusercontent.com/36380066/131727304-e6f62b42-3716-4e48-8892-585b20e7da59.png)

Y creamos nuestro balanceador.

![image](https://user-images.githubusercontent.com/36380066/131727625-76b6d278-7d2b-4174-b0e2-311596cf545d.png)

Como tercer paso crearemos nuestro servicio para esto nos dirigimos a Elastic Container Registry

![image](https://user-images.githubusercontent.com/36380066/131728267-f2dfdfb4-60be-4120-b849-fc87dd688936.png)

![image](https://user-images.githubusercontent.com/36380066/131728443-ac5d81e9-ef30-41f4-aa9f-7bd51f3d609d.png)

![image](https://user-images.githubusercontent.com/36380066/131728510-23a1555f-84ad-4043-98cd-74419f7a4cec.png)

![image](https://user-images.githubusercontent.com/36380066/131728594-5d556053-8a61-4d6e-a934-409979442483.png)

![image](https://user-images.githubusercontent.com/36380066/131728657-2a4e5951-e23d-4804-8c8b-b98f0dd8d930.png)

![image](https://user-images.githubusercontent.com/36380066/131728710-d2d9b6c0-f457-470f-b8d8-12fa16c777ad.png)

![image](https://user-images.githubusercontent.com/36380066/131728872-93b3bbee-43de-45f3-b134-ba986fc9ecc7.png)

![image](https://user-images.githubusercontent.com/36380066/131728924-d9404ec8-c00c-4916-9c54-a83c056e65dc.png)

![image](https://user-images.githubusercontent.com/36380066/131728986-380a9a03-4e83-4f45-8cff-116c27c93267.png)

Como Cuarto paso iremos a nuestro balanceador de carga, seleccionaremos el grupo de seguridad en el cual esta trabajando y modificaremos las reglas de entrada

![image](https://user-images.githubusercontent.com/36380066/131729185-0ecfcee1-5b73-4b44-be4f-cf8e45db953d.png)

![image](https://user-images.githubusercontent.com/36380066/131729238-3df7a9a3-8b01-4bf0-a1c7-f28e1a603e62.png)

En esté solo tendremos 3 reglas de entrada:

1- regla https: 0.0.0.0/0 

2- regla http: 0.0.0.0/0

3- All tcp: aquí seleccionamos el grupo de seguridad del balanceador, para que todas las peticiones que lleguen, accedan a traves del balanceador

![image](https://user-images.githubusercontent.com/36380066/131729965-5c009168-8c59-413d-a4bb-e74d6d413044.png)

![image](https://user-images.githubusercontent.com/36380066/131729732-125a9505-da3f-48d7-bdc7-dfabffde74e8.png)

Guardamos las reglas que acabamos de modificar 

![image](https://user-images.githubusercontent.com/36380066/131730092-a1be1452-805a-4de7-9fc4-e7a6e926cafc.png)

Como quinto paso agregaremos Sticky Session

Sticky Session: 

De forma predeterminada, un Classic Load Balancer enruta cada solicitud de forma independiente a la instancia registrada con la carga más pequeña. Sin embargo, puede utilizar la función de sesión permanente (también conocida como afinidad de sesión), que permite que el equilibrador de carga vincule la sesión de un usuario a una instancia específica. Esto asegura que todas las solicitudes del usuario durante la sesión se envíen a la misma instancia.

La clave para administrar sesiones pegajosas es determinar cuánto tiempo su balanceador de carga debe enrutar consistentemente la solicitud del usuario a la misma instancia. Si su aplicación tiene su propia cookie de sesión, puede configurar Elastic Load Balancing para que la cookie de sesión siga la duración especificada por la cookie de sesión de la aplicación. Si su aplicación no tiene su propia cookie de sesión, puede configurar Elastic Load Balancing para crear una cookie de sesión especificando su propia duración de permanencia.

Elastic Load Balancing crea una cookie, denominada AWSELB, que se utiliza para asignar la sesión a la instancia.

Para configurarlo nos vamos al apartado EC2 en Target Group 

![image](https://user-images.githubusercontent.com/36380066/131731130-20ed23c8-4140-424f-b448-d7f1a07dbdc9.png)

![image](https://user-images.githubusercontent.com/36380066/131731160-eb0a2f4e-aa1f-45b8-a76a-bdb0f559a7b5.png)

![image](https://user-images.githubusercontent.com/36380066/131731233-f117e53e-a55a-4954-8e93-91d7b82ed7ce.png)

![image](https://user-images.githubusercontent.com/36380066/131731275-dd686492-6d88-4cdc-9de0-0693220e0b84.png)

Y guardamos los atributos que modificamos

![image](https://user-images.githubusercontent.com/36380066/131731389-963ed422-ab14-47a7-84b7-4dca06cdc6b7.png)

Como sexto y último paso, asociamos nuestro balanceador de carga a nuestro dominio previamente ya creado, 
para esto nos vamos a ROUTE 53 y ahí seleccionamos el dominio en el cual estaremos trabajando

![image](https://user-images.githubusercontent.com/36380066/131731723-608ce515-7241-4500-8242-70fd9df5b147.png)

![image](https://user-images.githubusercontent.com/36380066/131731670-65874d0d-b7af-40bb-9009-5f2ff8350c3d.png)

![image](https://user-images.githubusercontent.com/36380066/131731606-60270fe5-f6f6-4026-aba9-4c8a35f85012.png)

Tendremos de está manera corriendo nuestro sistema.




