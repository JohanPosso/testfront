# Docker, Node.js, Vue.js, AWS, Sequelize, GithubAction

_Para este desarrollo se espera que construyas una base de datos en AWS, extraigas información de la API de CoinMarketCap mediante una función Lambda escrita en Python, y luego despliegues una página web donde muestres un gráfico con la información extraída utilizando un contenedor Docker y, finalmente, implementes un flujo de trabajo de integración continua con GitHub Actions_

![diagrama drawio](https://github.com/JohanPosso/testfront/assets/74286128/2593456b-ca8c-4892-8781-2fdbb8cb0342)


## Comenzando 🚀

_Estas instrucciones te permitirán obtener una copia del proyecto en funcionamiento en tu máquina local para propósitos de desarrollo y pruebas._



### Construcción de una base de datos en AWS
Crea una base de datos en AWS utilizando: **Amazon RDS**


<img width="788" alt="Captura de pantalla 2023-06-12 a la(s) 4 58 11 p  m" src="https://github.com/JohanPosso/testfront/assets/74286128/f2d7aac4-bf72-499d-a26b-95c329aa086b">


Usa un administración de bases de datos para facilitar la tarea de mantenimiento y optimización de las bases de datos

Pruebo la conexion a mi base de datos con **DBAVER**


<img width="788" alt="Captura de pantalla 2023-06-12 a la(s) 4 59 46 p  m" src="https://github.com/JohanPosso/testfront/assets/74286128/a3ed573d-aa7c-4e90-9446-a3f91866ef35">



Crea una base de datos en **PostgreSQL**, luego crea una tabla con nombre **bitcoin_prices** con las siguientes columnas:

```
id (serial)
price (float)
timestamp (timestamp)
```

### Extracción de información desde la API de CoinMarketCap a traves de un script en Python

Importa los modulos necesarios para la ejecucion de un script en python

Establece los parametros de conexion de tu base de datos

![conexion](https://github.com/JohanPosso/testfront/assets/74286128/51091d36-769c-4c37-af7d-99e7a013ef62)

Usa un cursor para ejecutar consultas a tu base de datos, usa request para peticiones a la **API** y recuperar el precio del Bitcoin

![cursor](https://github.com/JohanPosso/testfront/assets/74286128/47d47b3e-4b82-4afc-9314-b763a2d896cf)


Ejecuta una consulta a la base de datos para insertar el precio del Bitcoin


![consulta](https://github.com/JohanPosso/testfront/assets/74286128/f500977e-fc65-4776-872b-d29b02ad4b79)

Aqui el codigo completo: 

![python](https://github.com/JohanPosso/testfront/assets/74286128/751b983a-8b4b-4e8e-a59e-aa9bc8d3c207)

### Crea una funcion Lambda en AWS

Confugura tus variables de entorno, en la pestaña de configuracion, despues opcion variables de entorno

Importa el script que anteriormente se hizo en python, comprimelo en un archivo zip y cargalo en la funcion Lambda en la opcion **Cargar desde**


<img width="1282" alt="Captura de pantalla 2023-06-12 a la(s) 3 53 01 a  m" src="https://github.com/JohanPosso/testfront/assets/74286128/818af7fb-7049-43fc-9225-690df63e8ca0">

Deploya el script en la opcion **Deploy** y ejecuta tu script en la opcion **Test**

Al ejecutar **Test** la funcion se ejecuta, realiza una conexion a la base de datos, hace una peticion a la API de **CoinMarketCap** y retorna un dato JSON con el precio del Bitcoin y la fecha/hora que tenia ese valor en el momento

Si todo salio exitoso deberia retornar un dato JSON, y haber almacenado los datos en la base de datos


<img width="1058" alt="Captura de pantalla 2023-06-12 a la(s) 3 58 13 a  m" src="https://github.com/JohanPosso/testfront/assets/74286128/c57f7f58-da5d-415d-bed0-b1cfd0861e35">

```
{
  "statusCode": 200,
  "body": "Precio del Bitcoin extraído y almacenado exitosamente."
}
```

Revisa tu administrador de base datos donde ya tendrias que tener insertado el dato enviado desde tu funcion Lambda


<img width="464" alt="Captura de pantalla 2023-06-12 a la(s) 4 18 57 a  m" src="https://github.com/JohanPosso/testfront/assets/74286128/a0b83a26-61ba-4a80-98a2-6788dc3ce080">


Regresa a tu funcion  a tu funcion Lambda y en el menu de la funcion, dirigite a Configuracion y luego a desencadenadores para crear un evento que se ejecute cada 6 horas desde la funcion lambda, esto tambien se puede hacer directamente desde el servicio **Amazon EventBridge**

Programa un evento un trigger para que se ejecute cada 6 horas, es decir que cada 6 horas se ejecutara tu funcion Lambda automaticamente e insertara los datos a la base de datos


<img width="1130" alt="Captura de pantalla 2023-06-12 a la(s) 4 04 25 a  m" src="https://github.com/JohanPosso/testfront/assets/74286128/223ce549-c75a-4604-94e4-9196bf9c2266">

## Despliega una página web que muestre un gráfico con la información extraída de la base de datos

Para esta seccion creamos dos entorno para el desarrollo, el **Backend** que tendra la logica, conexion a la base de datos y recuperar la informacion de la tabla que contiene el precio del Bitcoin y el **FrontEnd** quien se encargara de de hacer una peticion al Backend para cargar los datos recuperados y exponerlos visualmente en un grafico

Para esto realizaremos el Backend en **Node.js** y el Frontend en **Vue.js**

### BACKEND

Inicia un proyecto con Node con el siguiente comando:

```
npm init
```
Se creara el siguiente archivo:

```
package.json
```
Instalamos las dependencias que utilizaremos de acuerdo a lo que se requiere para el proyecto, para la instalacion se ejecuta el comando:

```
git install <nombre de la dependencia o paquete a instalar>
```
Instalaremos los siguientes paquetes:

```
git install body-parser --> Analiza los cuerpos de las solicitudes HTTP entrantes y los convierte en un formato utilizable en el objeto
git install cors --> Permite que un servidor responda a solicitudes desde un dominio diferente al dominio de la página que realiza la solicitud
git install dotenv --> Carga las variables de entorno desde un archivo .env en tu proyecto Node.js
git install express --> Es un marco de aplicación web de Node.js que facilita la creación de API y aplicaciones web
git install nodemon --> Herramienta de desarrollo que supervisa los cambios en los archivos de tu proyecto Node.js
git install pg --> Paquete de Node.js que proporciona una interfaz para conectarse y trabajar con la base de datos PostgreSQL
git install pg-hstore --> Ayuda a manejar la serialización y deserialización de objetos JSON en columnas hstore en PostgreSQL
git install sequelize --> Simplifica la interacción con la base de datos. Proporciona una abstracción sobre las consultas SQL y permite definir modelos
```
creamos un archivo Index.js en la raiz del proyecto para levantar un servidor
```
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`)
})
```

Creamos una conexion a nuestra base de datos usando Sequelize

![conesequelize](https://github.com/JohanPosso/testfront/assets/74286128/0e5f687f-eaaf-4df0-9fbf-ab4f309fab2c)

Creamos un modelo, para interactuar con la base de datos

```
const { DataTypes } = require("sequelize");
const sequelize = require("../database/conexion");

const BtcPrices = sequelize.define(
  "bitcoin_prices",
  {
    id: {
      type: DataTypes.INTEGER,
      primaryKey: true,
      autoIncrement: true,
    },
    price: {
      type: DataTypes.FLOAT,
      allowNull: false,
    },
    timestamp: {
      type: DataTypes.DATE,
      defaultValue: sequelize.literal("CURRENT_TIMESTAMP"),
    },
  },
  { timestamps: false }
);

module.exports = BtcPrices;
```
Creamos un controlador para que a traves del modelo devuelva todos los datos de la tabla relacionada al modelo

```
const ModelBtc = require("../models/BtcPrices");

const readData = async (req, res) => {
  try {
    const dataRead = await ModelBtc.findAll({ order: [["id", "ASC"]] });
    res.json(dataRead);
  } catch (error) {
    res.status(400).send(error);
  }
};

module.exports = { readData };
```

Creamos una funcion para alojar diferentes rutas en caso de que el proyecto crezca sea mucho mas manejable

```
const btcRoute = require("./btc.route");

const allRoutes = (app) => {
  app.use("/api/v1/", btcRoute);
};

module.exports = allRoutes;
```

Finalizamos la creacion del endpoint creando la ruta a la cual se le va a hacer peticion desde el lado del cliente

```
const router = require("express").Router();
const controller = require("../controllers/controller");

// Read data
router.get("/read", controller.readData);

module.exports = router;
```

Usare una herramienta que simula en comportamiento del cliente para peticiones **POSTMAN**

Se hace una peticion GET como se configuro en el controlador para obtener los datos que contiene nuesta tabla en la base de datos


<img width="1023" alt="Captura de pantalla 2023-06-12 a la(s) 4 54 56 p  m" src="https://github.com/JohanPosso/testfront/assets/74286128/db7d9145-6783-4785-88a7-64e8cbc936c5">


```
GET : http://localhost:3000/api/v1/read
```

De esta manera finalizamos exitosamente con la creacion de nuestro endpoint para extraer los datos alojados en la base de datos


### FRONTEND

Para el desarrollo del Frontend utilizamos un framework de Javascript, Vue.js

Empezamos instalando globalmente vue/cli

```
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```

Seguido del comando 

```
vue create <nombre de tu proyecto>
```

Instalamos las dependencias que usaremos para nuestro proyecto

```
  npm install chart.js
  npm install chartjs-adapter-date-fns
  vue add vuetify
```

Siguiente a la instalacion consumis la API o endpoint que se hizo anteriormente con Node, esto para recuperar la data y poder utilizar de forma visual para el usuario final

```
const response = await fetch("http://localhost:3000/api/v1/read");
        const data = await response.json();
        const prices = data.map((item) => ({
          x: new Date(item.timestamp),
          y: item.price,
        }));
```
Importamos los modulos necesarios de Chart.js para la creacion de nuestra grafica que muestre el valor del Bitcoin respecto al tiempo

```
import {
  Chart,
  ScatterController,
  TimeScale,
  LinearScale,
  PointElement,
  LineController,
  LineElement,
} from "chart.js";
import "chartjs-adapter-date-fns";

Chart.register(
  ScatterController,
  TimeScale,
  LinearScale,
  PointElement,
  LineController,
  LineElement
);
```
Ejecutamos el proyecto el cual internamente levanta un servidor en un puerto disponible, y de este esta manera visualizamos los datos que extraimos de la API, en un grafico


<img width="1438" alt="Captura de pantalla 2023-06-12 a la(s) 6 04 21 a  m" src="https://github.com/JohanPosso/testfront/assets/74286128/af5aa529-e745-4337-b9e4-d6a66778c069">



### Instalación de Docker 🔧

_Realizare una serie de ejemplos paso a paso que te dice lo que debes ejecutar para tener un entorno de desarrollo ejecutandose con docker_


**Verificar requisitos:** Asegúrate de que tu Mac cumpla con los requisitos mínimos de Docker. Debes tener macOS 10.14 o una versión posterior y un procesador Intel compatible.

**Descargar Docker:** Visita el sitio web oficial de Docker (https://www.docker.com) y descarga Docker Desktop para Mac.

**Instalar Docker:** Ejecuta el archivo de instalación descargado y sigue las instrucciones del asistente de instalación para completar la instalación.

**Iniciar Docker:** Una vez instalado, busca Docker en tus aplicaciones y ejecútalo. Docker se iniciará como una aplicación de fondo.

**Verificar la instalación:** Abre una terminal y ejecuta el comando docker --version para confirmar que Docker se ha instalado correctamente.

Una vez instalado Docker en tu Mac, puedes utilizarlo para trabajar en tu proyecto localmente.

Recuerda que Docker también proporciona una interfaz gráfica llamada Docker Dashboard, que te permite administrar y monitorear tus contenedores de manera visual. Puedes encontrar más información y documentación detallada en el sitio web oficial de Docker.



**Preparar tu proyecto:** Asegúrate de que tu proyecto Node.js esté configurado correctamente y tenga un archivo package.json que incluya todas las dependencias necesarias.

**Crear un archivo Dockerfile:** En la raíz de tu proyecto, crea un archivo llamado Dockerfile (sin extensión). Este archivo contendrá las instrucciones para construir la imagen del contenedor.

**Especificar la imagen base:** En el Dockerfile, especifica la imagen base que deseas utilizar. Por ejemplo, puedes utilizar la imagen oficial de Node.js para tu versión específica de Node.js, como node:14. Esto proporcionará un entorno Node.js dentro del contenedor.

**Copiar archivos al contenedor:** Utiliza la instrucción COPY en el Dockerfile para copiar los archivos de tu proyecto al contenedor. Puedes copiar todo el proyecto o solo los archivos necesarios. Por ejemplo: COPY . /app copiará todos los archivos y directorios al directorio /app dentro del contenedor.

**Instalar dependencias:** Utiliza la instrucción RUN en el Dockerfile para ejecutar los comandos necesarios para instalar las dependencias de tu proyecto. Por ejemplo: RUN npm install ejecutará el comando npm install dentro del contenedor para instalar las dependencias.

**Exponer el puerto:** Si tu backend Node.js escucha en un puerto específico, utiliza la instrucción EXPOSE en el Dockerfile para especificar el puerto que deseas exponer. Por ejemplo: EXPOSE 3000 expondrá el puerto 3000 dentro del contenedor.

**Especificar el comando de inicio:** Utiliza la instrucción CMD en el Dockerfile para especificar el comando que se ejecutará cuando se inicie el contenedor. Por ejemplo: CMD ["npm", "start"] ejecutará el comando npm start dentro del contenedor.

```
Dockerfile
___________________

FROM node:20

WORKDIR /app

COPY . .

RUN npm install

EXPOSE 8080

CMD [ "npm", "run", "start" ]
```

**Construir la imagen:** Abre una terminal en la ubicación del Dockerfile y ejecuta el siguiente comando para construir la imagen del contenedor:


```
docker build -t nombre-imagen .
```

Reemplaza nombre-imagen con el nombre que desees para tu imagen.

**Ejecutar el contenedor:** Una vez que se haya construido la imagen, puedes ejecutar un contenedor basado en esa imagen con el siguiente comando:


```
docker run -p puerto-host:puerto-contenedor nombre-imagen

```
Reemplaza puerto-host con el puerto en el que deseas que el contenedor escuche en tu máquina host. Puedes asignar el mismo puerto o utilizar un puerto diferente. Reemplaza puerto-contenedor con el puerto en el que tu backend Node.js está escuchando dentro del contenedor. Reemplaza nombre-imagen con el nombre de la imagen que construiste en el paso anterior.

¡Listo! Ahora deberías tener tu proyecto ejecutándose en un contenedor Docker. Puedes acceder a él a través del puerto especificado en tu máquina host.


Puedes usar **Docker Desktop** para la adminstracion de tus imagenes y contenedores


<img width="1260" alt="Captura de pantalla 2023-06-12 a la(s) 6 22 27 a  m" src="https://github.com/JohanPosso/testfront/assets/74286128/6a3b0496-70ad-48d8-b57c-3a897b0a3a4a">



Ademas puedes usar **Docker Hub** es un servicio en la nube que ofrece un registro de imágenes de contenedores Docker. Es una plataforma centralizada donde los desarrolladores pueden publicar, compartir y descargar imágenes de contenedores preconfiguradas para facilitar el despliegue de aplicaciones en entornos Docker.


<img width="1424" alt="Captura de pantalla 2023-06-12 a la(s) 6 26 05 a  m" src="https://github.com/JohanPosso/testfront/assets/74286128/0e871f6d-5136-4743-b725-0494d8291aa2">



## Ejecutando las pruebas con integración continua con GitHub Actions ⚙️

_Explica como ejecutar las pruebas automatizadas para este sistema_

### Analice las pruebas end-to-end 🔩

_Explica que verifican estas pruebas y por qué_

```
Da un ejemplo
```

### Y las pruebas de estilo de codificación ⌨️

_Explica que verifican estas pruebas y por qué_

```
Da un ejemplo
```

## Despliegue 📦

_Agrega notas adicionales sobre como hacer deploy_

## Construido con 🛠️

_Menciona las herramientas que utilizaste para crear tu proyecto_

* [Dropwizard](http://www.dropwizard.io/1.0.2/docs/) - El framework web usado
* [Maven](https://maven.apache.org/) - Manejador de dependencias
* [ROME](https://rometools.github.io/rome/) - Usado para generar RSS


## Autor ✒️

* **Johan Posso** - *Desarrollo Completo* - [johanposso](https://github.com/johanposso)

---
⌨️ con ❤️ por [johanposso](https://github.com/johanposso) 😊
