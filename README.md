# integration-sonarqube-kubernetes
# Título
Integración de SonarQube en Kubernetes

## Comenzando 🚀
_Estas instrucciones te permiten obtener una copia del proyecto en funcionamiento en tu máquina local para propósitos de desarrollo y pruebas._

### Pre-requisitos 📋
_Que cosas necesitas para instalar el software y como instalarlas_
- Manejar la consola de PowerShell para la ejecución de comandos.
- Manejar el editor Notepad o Bloc de Notas para la creación de los archivos de despliegue.


### Instalación 🔧 Pruebas ⚙️ y Despliegues 📦
INTEGRACIÓN DE SONARQUBE EN KUBERNETES:
Para crear una contaminación, debe seleccionar un nodo que desee reservar para SonarQube. Utilice el siguiente comando para obtener una lista de todos los nodos adjuntos a su clúster de Kubernetes.
<img width="359" height="44" alt="image" src="https://github.com/user-attachments/assets/31528619-1283-49a9-bfb5-110a35a13bad" />

Seleccione un nodo de la salida de este comando y cree una contaminación personalizada con el siguiente comando.
<img width="577" height="32" alt="image" src="https://github.com/user-attachments/assets/410160a5-67b6-4bbc-a974-8bcb89a49b65" />

Esta contaminación garantiza que no se programen pods adicionales en este nodo.

Ignorando esta mancha para SonarQube:
Para permitir que la implementación de SonarQube ignore la contaminación creada anteriormente, agregue la siguiente sección al archivo values.yaml.
<img width="215" height="155" alt="image" src="https://github.com/user-attachments/assets/06e18c7d-1994-429d-960f-c63367eb58b5" />

Dependiendo del nombre de su contaminación, es posible que deba ajustar la clave en consecuencia.
Etiquetas de nodo:
Como se describe en la sección anterior de incidencias y tolerancias, para mayor estabilidad, se recomienda vincular la implementación de SonarQube a un nodo en su clúster. Con un nodo reservado para SonarQube, debe etiquetar este nodo para que el planificador de Kube lo seleccione en la asignación del pod.

Etiquetar un nodo:
Etiquete el nodo para el que definió previamente una contaminación con el siguiente comando:
Kubectl label node minikube sonarqube=true
<img width="497" height="31" alt="image" src="https://github.com/user-attachments/assets/c7b40c76-b496-4a94-861a-d1c3eeb0a74c" />

Vincular implementación a etiqueta:
Para permitir que SonarQube se programe en nodos con esta etiqueta específica, agregue la siguiente sección al archivo values.yaml.
<img width="195" height="209" alt="image" src="https://github.com/user-attachments/assets/e3ba053c-2e63-4220-912a-bb84b6a017ea" />


Al combinar la selección de nodos con contaminaciones y tolerancias, SonarQube puede ejecutarse solo en un nodo específico independientemente del resto de su software en su clúster de Kubernetes. Esto da como resultado una mejor estabilidad y rendimiento de SonarQube.
Creamos un namespace:
Kubectl create namespace sonarqube
<img width="450" height="37" alt="image" src="https://github.com/user-attachments/assets/10405e0d-3451-4c89-87e0-47b619667ea0" />

<img width="976" height="431" alt="image" src="https://github.com/user-attachments/assets/122e125c-93f8-47f5-b02b-bf625e2331f0" />


Persistencia:
SonarQube viene con un paquete de ElasticSearch, y dado que ElasticSearch tiene estado, también lo es SonarQube. Hay una opción para conservar los índices de ElasticSearch en un volumen
persistente, pero con las operaciones regulares de eliminación por parte del clúster de Kubernetes, estos índices pueden corromperse.
Habilitar la persistencia reduce el tiempo de inicio de SonarQube Pod, pero corre el riesgo de corromper su índice de ElasticSearch. Puede habilitar la persistencia agregando lo siguiente a values.yaml.
<img width="207" height="233" alt="image" src="https://github.com/user-attachments/assets/d2191fa9-b1e1-4523-92cb-5dd92b5ee346" />
  
Si deja la persistencia deshabilitada, se prolongará el tiempo de inicio hasta que SonarQube esté disponible, pero no perderá ningún dato, ya que SonarQube conservará todos los datos en la base de datos.

Certificado personalizado:
Cuando trabaja con su propia CA o en un entorno que usa certificados autofirmados para su plataforma de repositorio de código, puede crear un secreto que contenga este certificado y agregar este certificado al almacén de confianza de Java dentro de la implementación de SonarQube directamente durante la implementación.
Para habilitar este comportamiento, agregue lo siguiente a su archivo value.yaml.

<img width="193" height="216" alt="image" src="https://github.com/user-attachments/assets/5d08ad99-e80b-41db-ae26-99b5e06aacf2" />


Modifica el servicio de SonarQube para escribir balanceador de carga en el archivo values.yaml.

<img width="263" height="309" alt="image" src="https://github.com/user-attachments/assets/38d4c0b1-2372-419e-a5b2-74d444c94fcc" />

<img width="600" height="279" alt="image" src="https://github.com/user-attachments/assets/c2680e50-2256-4c7f-870e-b355cb7f0463" />


Creamos el archivo sonar-data.yaml.

<img width="256" height="280" alt="image" src="https://github.com/user-attachments/assets/917fd26c-9ebf-46d5-8061-9e1204468cc1" />


Creamos el archivo sonar-extensions.yaml.

<img width="339" height="274" alt="image" src="https://github.com/user-attachments/assets/251e0072-76fc-415c-b421-d6ae1a08fb37" />

<img width="440" height="269" alt="image" src="https://github.com/user-attachments/assets/1684133b-854f-449b-9587-3c7890a83f03" />


kubectl apply –f sonar-data.yaml
kubectl apply –f sonar-extensions.yaml

<img width="464" height="63" alt="image" src="https://github.com/user-attachments/assets/4987121e-4a6d-4f97-894b-0b9dbce58d95" />


Crear una implementación de SonarQube mediante el archivo sonar-deployment.yaml.

<img width="644" height="820" alt="image" src="https://github.com/user-attachments/assets/aade5f83-875e-422e-8b19-ccfa42a3c719" />

<img width="274" height="92" alt="image" src="https://github.com/user-attachments/assets/220c3a0b-178f-49d3-a05b-08b806b991c6" />

<img width="461" height="289" alt="image" src="https://github.com/user-attachments/assets/85a7e810-a411-4ba6-a5e7-db447804d82e" />


Copiamos el archivo sonar-deployment.yaml a la ruta C:\Users\6002336\.kube

<img width="843" height="60" alt="image" src="https://github.com/user-attachments/assets/577ba8c4-dc97-4f1a-8f55-dacb151fe5a8" />


Publique SonarQube a través del servicio Kubernetes:
Creamos el archivo sonar-svc.yaml.

<img width="277" height="344" alt="image" src="https://github.com/user-attachments/assets/f00ae182-3a7a-4686-afb4-372d14ac7dc9" />

<img width="461" height="403" alt="image" src="https://github.com/user-attachments/assets/e6ccd39b-2c6b-4da0-8507-9d1ce23ee4e4" />


kubectl apply –f sonar-svc.yaml

<img width="439" height="33" alt="image" src="https://github.com/user-attachments/assets/4932deda-4e0b-4c22-bb4f-92a1b919c627" />


kubectl get svc
<img width="545" height="64" alt="image" src="https://github.com/user-attachments/assets/2e0024f7-3662-4e06-9714-4d3c6acf3b63" />




## Licencia 📄
Bajo licencia GNU General Public License v3.0
