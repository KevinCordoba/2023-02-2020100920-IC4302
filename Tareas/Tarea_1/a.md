# Tarea Corta-Observability
#### **Juan Ernesto Mallma Zamora - 2022285164**
#### **Kevin Córdoba Chevez - 2020100920**
##
# Índice
1. [Introducción](#1introducción)
2. [Guía de instalación y uso](#2-guía-de-instalación-y-uso)
3. [Configuración de las herramientas](#3-configuración-de-las-herramientas)
4. [Pruebas de cargas realizadas](#4-pruebas-de-cargas-realizadas)
5. [Conclusiones y recomendaciones](#5-conclusiones-y-recomendaciones)
##
# 1.Introducción

  El objetivo principal de esta tarea corta del curso es desarrollar un sistema de observabilidad que genere métricas y gráficas para extraer información de las bases de datos, a través de kubernetes con Helm charts de Bitnami configurados para las herramientas. Entre las herramientas que hemos instalado se encuentran Prometheus para la exportación de métricas, Grafana para la graficación de los datos y Gatling para la generación de pruebas de carga en las bases de datos.

  Para el sistema de observabilidad que se pide en la tarea, se debieron crear e instalar el chart _app_ para la aplicación intermedia que va a conectarse con Gatling y las bases de datos, el chart _bootstrap_ para los _namespaces_, se instalo también el monitoring-stack para realizar el monitoreo con _prometheus_ y _grafana, databases_ para realizar el _deployement_ de los motores de bases de datos proporcionados por bitnami y por último se tiene al chart grafana-config que se encarga de automatizar la configuración de los _dashboards_ que se van a graficar.


# 2. Guía de instalación y uso:

Primero, se debe abrir la carpeta de la tarea en un editor de texto como Visual Studio Code.

![](https://hackmd.io/_uploads/Bk6_HdYCn.png)


Seguidamente se procede a iniciar desde la terminal el distro WSL de Linux para Windows, a través del comando: wsl.exe

![](https://hackmd.io/_uploads/rJUFSdKAh.png)


Una vez se inicializa la máquina virtual y el _Docker_ ya está corriendo, por lo que ahora se necesita activar el _cluster_ de _kubernetes_ en _minikube_ con el comando: minikube start

![](https://hackmd.io/_uploads/HkaFB_FR2.png)


A continuación, se debe obtener la contraseña para poder accesar a Grafana, con el comando: kubectl get secret grafana-admin-credentials -n monitoring -o=jsonpath='{.data.GF\_SECURITY\_ADMIN\_PASSWORD}' | base64 --decode; echo

![](https://hackmd.io/_uploads/rkFqButRn.png)


Una vez se tenga la contraseña se procede a realizar el forward de la siguiente manera kubectl port:

![](https://hackmd.io/_uploads/rJ-ir_K0h.png)


Accediendo al url http://127.0.0.1:3000, se debe iniciar sesión con el nombre de usuario _admin_ e ingresando la contraseña obtenida anteriormente.

![](https://hackmd.io/_uploads/S1tiruFCh.png)


Al iniciar sesión se muestran los dashboards de las bases de datos que se encuentran disponibles en el sistema.

![](https://hackmd.io/_uploads/rJE2SdtAn.png)


![](https://hackmd.io/_uploads/Sy9nSOKRh.png)


# 3. Configuración de las herramientas:

La configuración de la aplicación intermedia fue realizada a traves de python, y una imágen de docker con el archivo Dockerfile configurado de la siguiente manera:

![](https://hackmd.io/_uploads/r1dX5dYC2.png)


Este API además importo las libreria del framework de flask y el de prometheus_client para la exportación de métricas de solicitudes http. El programa en python se muestra a continuación:

![](https://hackmd.io/_uploads/rJIVqOYAn.png)


Para la instalación de prometheus y el grafana operator, se llevó a cabo una configuración a través del Helm chart monitoring-stack, en el cual se realizaba un _pull_ del repositorio con la imagen proporcionada por bitnami. En el archivo Chart.yaml del monitoring-stack chart se definieron los siguientes parámetros que se pueden observar a continuación:

![](https://hackmd.io/_uploads/Sk5kIOtC3.png)


Para que se instalaran los charts de bitnami se tuvo que ingresar el comando: helm dependency build. Una vez este comando se ejecutaba se guardaron los archivos _.tgz_ del grafana-operator y el kube-prometheus en la carpeta charts del monitoring stack. Por otro lado, el archivo _values.yaml_ se uso para configurar los parámetros del grafana y el prometheus, usando el namespace monitoring que debió ser creado a partir del comando helm kubectl create namespace.

![](https://hackmd.io/_uploads/rk-lUuKA2.png)


En lo que respecta al _deployement_ de las bases de datos, estas fueron configuradas en el Helm chart databases, con la base de datos de mariadb de bitnami teniendo la siguiente configuración en el values.yaml:

![](https://hackmd.io/_uploads/BJFl8uFAh.png)


De igual manera que se llevo a cabo con prometheus y grafana, se obtuvo la base de datos de bitnami charts. Para lo cual se debió hace un helm dependency update en la carpeta del databases y se definió el dependency en _Chart.yaml_ de la siguiente manera:

![](https://hackmd.io/_uploads/HJ6ZUOFRn.png)

Los dashboards de grafana para las bases de datos se configuraron en el Helm chart grafana-config, instalando los archivos json de la página de grafana en la carpeta dashboards. Los dashboards a crear automáticamente fueron especificados en el archivo _values.yaml_ de la siguiente manera:

![](https://hackmd.io/_uploads/r1BG8uKCn.png)


# 4. Pruebas de cargas realizadas:

  El dataset seleccionado para utilizar en este trabajo sobre observabilidad fue acerca de *E-commerce Data*, el cual contiene transacciones reales de minoristas en el Reino Unido. Dicho dataset cuenta con los siguientes campos: número de recibo, código de *stock*, Descripción, cantidad, fecha del recibo, precio de unidad, id del cliente y país.
  Dicho dataset se obtuvo de la plataforma digital Kaggle, en la siguiente dirección: https://www.kaggle.com/datasets/carrie1/ecommerce-data
  
  Para la realización de la simulación con Gatling usando este dataset, se debio configurar un programa en scala que debía leer el archivo y definiera los parametros de la simulación con los usuarios y un intervalo específico como se puede apreciar a continuación en la imágen del código:
  
  ![](https://hackmd.io/_uploads/rkZvMtFC3.png)

 
# 5. Conclusiones y recomendaciones:

  Con base en lo mencionado anteriormente es posible apreciar la utilidad que otorga la observabilidad en sistemas, con el uso de las herramientas adecuadas permite la extracción de la información contenida en una base de datos para tranformarla en gráficas gracias a las métricas generadas, lo que permite una mayor y mejor comprensión del comportamiento de los mismos. 
  Como parte de las herramientas previamente mencionadas cabe resaltar la utilidad de Prometheus que permite exportar las métricas, Grafana que ayuda a utilizarlas para poder graficar los datos y Gatling que permite la generación de pruebas de carga en las bases de datos indicadas.
  Se llevó a cabo la configuración de motores de bases de datos que permiten este proceso junto con la configuración de un sistema de monitoereo utilizando Prometheus y Grafana.
  
  Como recomendaciones se insta a utilizar datasets con gran volumen de datos para así contar con mayor cantidad de informaación con la que puede trabajar el sistema, creando gráficas más certeras que proporcionen mayor información de modo que se puedan obtener conclusiones más acertadas de ellas e información que permita predecir comportamientos futuros.