# Despliegue de una red Blockhain con Docker y Kubernetes

En este proyecto se muestra el despliegue de la red Blockchain a través de Minikube, se enseña un ejemplo mediante el cual se podrá ver cómo las transacciones realizadas quedan registradas en la Blockchain. Esta aplicación tiene su lógica almacenada dentro de la Blockchain, y a la que todos los miembros de la red tienen acceso.

### Requisitos

* Docker
* Kubectl
* Minikube
* Truffle
* MetaMask

### Setup blockchain

Antes de nada se debe clonar el repositorio:

`$ git clone https://github.com/effyrv/besu_k8s.git`

El primer paso para desplegar la red Blockchain es montar el volumen en el nodo de Minikube para que los archivos locales estén disponibles en cada uno de los contenedores del pod.

`$ minikube mount $HOME/besu_k8s:/besu_k8s`

Esta terminal se deja abierta y no se toca más.

En otra terminal, accedemos a la carpeta del repositorio:

`$ cd ~/besu_k8s`

Se aplica el manifiesto de Kubernetes, en este manifiesto se encuentra el deployment y el servicio.
Con esto los contenedores del pod descargarán la imagen de hyperledger besu y empezarán a trabajar en el despliegue de la red Blockchain. Además, a través del servicio NodePort los contenedores serán accesibles desde el exterior.

`$ kubectl apply -f hyperledger_besu.yaml`

La Blockchain en este punto ya se ha creado y los bloques se están produciendo. Para acceder a logs de los contenedores consulta la sección [logs](#logs)

### Setup DApp

Dentro del repositorio, se instalan las dependencias necesarias incluidas en el `package.json`:

`$ npm install`

Para lanzar la aplicación descentralizada y así comprobar como las transacciones quedan registradas en la Blockchain y que todos tienen acceso hay que acceder a la carpeta “dapp-galerias” y ejecutar el siguiente comando para realizar el despliegue de los contratos inteligentes en la red que se ha levantado con Kubernetes.

`$ truffle migrate --reset --network besuWallet`

Una vez hecho lo anterior se lanza la aplicación.

`$ npm run dev`

Para interactuar con la aplicación se pone una de las claves privadas de una cuenta de las que hay en el archivo `genesis.json` y se importa a MetaMask, que es una cartera digital y una extensión de navegador que permite a los usuarios interactuar con aplicaciones descentralizadas basadas en la tecnología Blockchain.

Una vez importada la red Blockchain a MetaMask e indicado el proveedor de la blockchain (`http://ip_k8s:puerto_nodo`) que en nuestro caso sería: `http://192.168.49.2:30000`, que será uno de los nodos de la red.

Ahora, si se pulsa en “Adquirir” sobre una obra, se abre MetaMask automáticamente para que aceptar la transacción que se va a registrar en la blockchain.

### Logs

Mostrar información sobre los pods en ejecución en un clúster de Kubernetes.

`$ kubectl get pods`

Mostrar información sobre los despliegues en un clúster de Kubernetes.

`$ kubectl get deployments`

Acceso a los logs del contenedor del nodo 1 (por defecto).

`$ kubectl logs nombre_del_pod`
