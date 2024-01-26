
- [Documentación de la API DoctorHelper.](#documentación-de-la-api-doctorhelper)
    - [Autenticación de todos los llamados.](#autenticación-de-todos-los-llamados)
    - [Comportamiento.](#comportamiento)
        - [(POST) UserValidation.](#post-uservalidation)
        - [(POST) Appointments.](#post-appointments)
        - [(POST) JSONSummary.](#post-jsonsummary)
        - [(POST) Status.](#post-status)
        - [(POST) JsonReturnSummary.](#post-jsonreturnsummary)
        - [(POST) AudioSummary.](#post-audiosummary)
        - [(POST) Subjective/Objective/Assessment.](#post-subjectiveobjectiveassessment)
        - [(POST) PlaningReturn.](#post-planingreturn)
        - [(POST) AudioEncounter.](#post-audioencounter)
        - [(POST) Correction.](#post-correction)
        - [(POST) CorrectionReturn.](#post-correctionreturn)
        - [(POST) Approved.](#post-approved)
        - [(POST) GetPatients.](#post-getpatients)
        - [(POST) PostPatient.](#post-postpatient)
        - [(POST) PostAppointment.](#post-postappointment)
        - [(POST) Delete](#post-delete)
        - [(POST) DataFrondEnd](#post-datafrondend)
        - [(POST) DeleteUser](#post-deleteuser)
        - [(POST) GetInactivePatients](#post-getinactivepatients)
        - [(POST) UpdatePatient.](#post-updatepatient)
        - [(POST) UpdateAppointment.](#post-updateappointment)
        - [(POST) DeleteAppointment](#post-deleteappointment)
        - [(POST) InactivatePatient](#post-inactivatepatient)
        - [(POST) ActivatePatient](#post-activatepatient)
        - [(POST) GetPractitionerPhoto.](#post-getpractitionerphoto)
        - [(POST) UpdatePractitionerPhoto.](#post-updatepractitionerphoto)
        - [(POST) WakeUpWord (Nuevo).](#post-wakeupword-nuevo)
  - [Errores extra](#errores-extra)
    - [Error de token](#error-de-token)
    - [Error de Action](#error-de-action)
    - [Error al encontrar Action](#error-al-encontrar-action)

# Documentación de la API DoctorHelper.

La API del backend para DoctorHelperAI posee un endpoint para el procesamiento de todas las consultas

**Endpoint de producción** : [**https://doctorhelperaibackend.<img src="https://doctorhelperaibackend.azurewebsites.net/)

**Endpoint de pruebas:** [**https://doctorhelperaibackend-qa.<img src="https://doctorhelperaibackend-dev.azurewebsites.net/)

**A la fecha actual (25/01/2024) se debe usar la Url de pruebas**

### Autenticación de todos los llamados.

La API utiliza autenticación mediante un token. En la primera solicitud se respondera el token de acceso, este se debe enviar por medio de un header llamado "Token" en las demás solicitudes

### Comportamiento.
>[!NOTE] Actualizaciones del 25 de enero de 2024
>1. Se corrigieron los errores de documentacion en:
>       - [(POST) DataFrondEnd](#post-datafrondend)
>       - [(POST) AudioEncounter.](#post-audioencounter)
>1. Se añado el llamado 
>       - [(POST) WakeUpWord.](#post-wakeupword)
> 
El comportamiento de cada consulta estará dado por un header llamado "Action", este header posee los siguientes valores:

- (POST) UserValidation
- (POST) Appointments
- (POST) JSONSummary
- (POST) Status
- (POST) JsonReturnSummary
- (POST) AudioSummary
- (POST) Subjective/Objective/Assessment
- (POST) PlaningReturn
- (POST) AudioEncounter
- (POST) Correction
- (POST) CorrectionReturn
- (POST) Approved
- (POST) GetPatients
- (POST) PostPatient
- (POST) PostAppointment
- (POST) Delete
- (POST) DataFrondEnd
- (POST) DeleteUser
- (POST) GetInactivePatients
- (POST) UpdatePatient
- (POST) UpdateAppointment
- (POST) DeleteAppointment
- (POST) InactivatePatient
- (POST) ActivatePatient
- (POST) GetPractitionerPhoto
- (POST) UpdatePractitionerPhoto
- (POST) GetPractitionerPhoto
- (POST) UpdatePractitionerPhoto
- (POST) WakeUpWord

A continuación, se mostrarán ejemplos de cómo se debe realizar cada una de las consultas en Python junto a un ejemplo de retorno de cada una de estas.

##### (POST) UserValidation.

En esta consulta el cliente le solicita a la API el token de acceso para el uso de los demás llamados, los headers para el llamado son los siguientes:
```python 
{
    'Action' : 'UserValidation', #Acción que se desea realizar
    'PractitionerMail' : 'ejemplo@empresa.com'
    'ADToken' : ADToken #Token que retorna el directorio activo
}
```
_Apartir de ahora no se volverá a explicar el funcionamiento del header Action_

Ejemplo:
```python 
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"
Headers =  {
            'Action' : 'UserValidation',
            'ADToken'  : 'dasf54as6da4dsa654d',
            'PractitionerMail' : 'ejemplo@empresa.com',
           }

Response = requests.post(Url, headers=Headers)
return Response.json()

```
La salida de esta consulta puede ser:
```python 
#Respuesta para un usuario valido
{'Token':Token,'status':'True'}

#Respuesta para un usuario no valido
{'status':'False'}

#Respuesta para un problema de verificación
{'Error en UserValidation': TypeError}
```
##### (POST) Appointments.

En esta consulta el cliente le solicita a la API el listado de todas las citas médicas que tiene un doctor en el día, los headers para el llamado son los siguientes:
```python
{
    'Action' : 'Appointments',
    'PractitionerId' : '0123456789', #Identificador
    'Time' : '2023-09-27' #Fecha de las citas medicas
}
```

Ejemplo:

```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"
Headers =  {
             'Action' : 'Appointments',
             'PractitionerId' : '47ea5939-fff5-439a-9668-f3c3974b4a88',
             'Time' : '2023-09-27'
           }

Response = requests.post(Url, headers=Headers)
return Response.json()
```


La salida de esta consulta puede ser:

- Respuesta valida del servidor <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/JSON-Appointments.txt)
- Respuesta valida del servidor, pero sin citas <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/JSON-Appointments%20Vacio.txt)
- Respuesta no valida
    ```python
    {'Error en Appointments': TypeError}
    ```
##### (POST) JSONSummary.

En esta consulta el cliente le soliciata a la API el resumen sobre el historial de un paciente, los headers para el llamado son los siguientes:
```python
{
    'Action' : 'JSONSummary',
    'PatientId' : '0123456789', #Identificador
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e' #Token para el uso
}
```
Ejemplo:
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : 'JSONSummary',
            'PatientId' : '0123456789',
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
            }

Response = requests.post(Url, headers=Headers)
return Response.json()
```

La salida de esta consulta pueden ser:
```python
#Respuesta valida
{"Time":"15.254"}

#Respuesta no valida por un error inesperado
{'Error en JSONSummary': TypeError}
```
##### (POST) Status.

En esta consulta el cliente le pide al servidor que actualice el estado de la consulta, indicando que ya se encuentra en progreso, los headers para el llamado son los siguientes:
```python
{
    'Action' : 'Status',
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
    'EncounterId' : 'c744967c-5e23-f68c-093d-c66b1d3ca861'
}
```
Ejemplo:
```python
import requests
Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : 'Status',
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
            'EncounterId' : 'c744967c-5e23-f68c-093d-c66b1d3ca861'
            }

Response = requests.post(Url, headers=Headers)
return Response.json()
```
La salida de esta consulta puede ser:
```python
#Respuesta valida
{'status':'True'}

#Respuesta no valida por un error inesperado
{'Error en Status': TypeError}
```
##### (POST) JsonReturnSummary.


En esta consulta el cliente le solicita a la API la información de un paciente junto a un resumen sobre su historial medico, este resumen va en el campo "Summary", este resumen debe pedirse con anterioridad en **JSONSummary** , los headers para el llamado son los siguientes:
```python
{
    'Action' : 'JsonReturnSummary',
    'PatientId' : '0123456789',
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
}
```
Ejemplo:
```python
import requests
Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : 'JsonReturnSummary',
            'PatientId' : '33d380ba-004a-4c52-bdbc-dbe0d2f68dce',
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
            }

Response = requests.post(Url, headers=Headers)
return Response.json()
```

La salida de esta consulta puede ser:

- Respuesta valida del servidor <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/JSON-JSONReturnSummary.txt)

- Respuesta no valida del servidor
    ```python
    {"Summary":"False"}
    ```
##### (POST) AudioSummary.

En esta consulta el cliente le solicita a la API un resumen en forma de audio sobre el historial del paciente seleccionado, el audio se envía en formato BINARIO en base64, esto dentro de un Json, donde , la estructura para los headers del llamado son los siguientes:
```python
{
    'Action' : 'AudioSummary',
    'PatientId' : '0123456789',
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
}
```
Ejemplo:
```python
import requests
import base64

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : 'AudioSummary',
            'PatientId' : '0123456789',
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
            }

Response = requests.post(Url, headers=Headers)
PhotoData = Response.json()
FilenName = PhotoData['FileName']
PhotoData64 = PhotoData['File']
# Decodifica el binario desde base64
PhotoData64 = base64.b64decode(PhotoData64)

withopen(FilenName, 'wb') as PhotoFile:
     PhotoFile.write(PhotoData64)
```
La respuesta puede ser.

- Respuesta valida del servidor <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/JSON-AudioSummary.txt)

- Respuesta no valida por un error inesperado
    ```python
    {'Error en AudioSummary': TypeError}
    ```
##### (POST) Subjective/Objective/Assessment.

En este llamado el cliente le envia a la API archivos de audio en formato m4a. Cada archivo de audio debe tener un nombre específico que sigue la estructura "PatientId-{Action}.m4a". Aquí, {Action} es un valor que corresponde a la acción específica, por ejemplo, "Subjective", además el valor de'DHAIRecord{Action}' también cumple esta regla . En el siguiente ejemplo, se muestra cómo realizar una consulta para la parte "Subjective", los headers para el llamado son los siguientes:
```python
{
    'Action' : 'Subjective', # Puede ser Objective,Assessment,etc…
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
}
```
Ejemplo
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Files = {'DHAIRecordPlanning': (1234567890-Planning.m4a', open(1234567890-Planning.m4a', 'rb'), 'audio/mp4')}

Headers =   {
            'Action' : 'Planning'
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
            }

Response = requests.post(Url, files=Files, headers=Headers)
return Response.json()
```
La respuesta puede ser.
```python
#Respuesta valida
{"Time": "465.16"}

#Respuesta no valida por error desconocido
{'Error en Planning': TypeError}
```
##### (POST) PlaningReturn.


En este llamado el cliente le pide a la API que le retorne el resumen de toda la consulta medica junto al bundle de esta consulta, los headers para el llamado son los siguientes:
```python
{
    'Action' : 'PlanningReturn',
    'PatientId' : '33d380ba-004a-4c52-bdbc-dbe0d2f68dce',
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
}
```
Ejemplo:
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =  {
            'Action' : 'PlanningReturn',
            'PatientId' : '33d380ba-004a-4c52-bdbc-dbe0d2f68dce',
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
           }

Response = requests.post(Url, headers=Headers)
return Response.json()
```

La respuesta puede ser.
```python
#Respuesta valida del servidor
{"Status":"True","Summary":Summary}

#Respuesta no valida del servidor
{"Error":TypeError,"Status": "Flase"}
```
##### (POST) AudioEncounter.

En esta consulta el cliente le solicita a la API un resumen en forma de audio sobre la cita medica del paciente, el audio se envía en formato m4a, con el nombre del paciente en el header "FileName", la estructura para el nombre del archivo es PatientId\_Encounter.m4a, los headers para el llamado son los siguientes:
```python
{
    'Action' : 'AudioEncounter',
    'PatientId' : '0123456789',
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
}
```
Ejemplo:
```python
import requests
import base64

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : 'AudioEncounter',
            'PatientId' : '0123456789',
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
            }

Response = requests.post(Url, headers=Headers)
PhotoData = Response.json()
FilenName = PhotoData['FileName']
PhotoData64 = PhotoData['File']
# Decodifica el binario desde base64
PhotoData64 = base64.b64decode(PhotoData64)

withopen(FilenName, 'wb') as PhotoFile:
     PhotoFile.write(PhotoData64)
```
La respuesta puede ser.
- Archivo de audio en formato m4a: 1234567890-Summary.m4a.

- Respuesta no valida por un error inesperado
    ```python
    {'Error en AudioEncounter': TypeError}
    ```
##### (POST) Correction.

En este llamado el cliente le envia a la API un archivo de audio en formato m4a. el nombre del archivo sigue la estructura "PatientId-Correction.m4a". Aquí se retornará el tiempo aproximado para la creación del bundle corregido, los headers para el llamado son los siguientes:
```python
{
    'Action' : Correction
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
}
```
Ejemplo:
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Files = {'DHAIRecordPlanning '': (1234567890-Planning.m4a', open('1234567890-Planning.m4a', 'rb'), 'audio/mp4')}

Headers =   {
                'Action' : 'Correction'
                'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
            }

Response = requests.post(Url, files=Files, headers=Headers)
return Response.json()
```
La respuesta puede ser.
```python
#Respuesta valida
{"Time": "465.16"}

#Respuesta no valida por error desconocido
{'Error en Correction ': TypeError}
```
##### (POST) CorrectionReturn.


En este llamado el cliente le pide a la API que le retorne el resumen de toda la consulta médica junto al bundle corregido de esta consulta, para funcionar correctamente debe llamar primero a **Correction** , los headers para el llamado son los siguientes:
```python
{
    'Action' : 'CorrectionReturn',
    'PatientId': '33d380ba-004a-4c52-bdbc-dbe0d2f68dce',
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
}
```
Ejemplo:
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =  {
                'Action' : 'CorrectionReturn',
                'PatientId' : '33d380ba-004a-4c52-bdbc-dbe0d2f68dce',
                'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
           }

Response = requests.post(Url, headers=Headers)
return Response.json()
```

La respuesta puede ser.

- Respuesta valida del servidor <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/JSON-Planning-Correction%20.txt)

- Respuesta no valida del servidor
    ```python
    {"Correction":str(e)}
    ```
##### (POST) Approved.

En esta consulta la API guardara toda la información en la base de datos, los headers para el llamado son los siguientes:
```python
{
    'Action' : 'Approved',
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
    'EncounterId' : 'c744967c-5e23-f68c-093d-c66b1d3ca861',
    'PatientId' : '0123456789'
}
```
Ejemplo
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : 'Approved',
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
            'EncounterId' : 'c744967c-5e23-f68c-093d-c66b1d3ca861',
            'PatientId' : '0123456789'
            }

Response = requests.post(Url, headers=Headers)
return Response.json()
```

La respuesta puede ser.
```python
#Respuesta valida
{"Status":"Approved"}

#Respuesta no valida por un error al eliminar carpeta del paciente
{'Status':'Error', 'Error': 'No se pudo eliminar la carpeta del paciente'}

#Respuesta no valida por un error inesperado
{'Status':'Error','Error': str(e)}
```
##### (POST) GetPatients.

En este llamado el cliente le pide a la API que le retorne todos los pacientes de un medico, los headers para el llamado son los siguientes:
```python
{
    'Action' : 'GetPatients',
    'PractitionerId' : '9876543210',
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
}
```
Ejemplo:

```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =  {
                'Action' : 'GetPatients',
                'PractitionerId': '9876543210',
                'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
           }

Response = requests.post(Url, headers=Headers)
return Response.json()
```

La respuesta puede ser.

- Respuesta valida del servidor <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/JSON-GetPatients.txt)

- Respuesta no valida del servidor
    ```python
    {'Error en GetPatients': TypeError}
    ```
##### (POST) PostPatient.

En este llamado el cliente le pide a la API que cree un nuevo paciente, los headers para el llamado son los siguientes:
```python
{
    'Action' : 'PostPatient',
    'Content-Type': 'application/json',
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
}
```

El json de este llamado es de la siguiente mandera:
```python
{
    "name":"Juan",
    "family":"Perez Perez",
    "phone":"1234567890",
    "gender":"male",
    "birthDate":"2021-09-09",
    "address":"Calle 1",
    "city":"Cali",
    "state":"Valle",
    "country":"Colombia"
    "PractitionerName" : "Dr. Evan94 Bins636",
    "PractitionerId" : "47ea5939-fff5-439a-9668-f3c3974b4a88"
}
```
Ejemplo:
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : 'PostPatient',
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
            'Content-Type': 'application/json'
            }

data = {
        "name":"Juan",
        "family":"Perez Perez",
        "phone":"1234567890",
        "gender":"male",
        "birthDate":"2021-09-09",
        "address":"Calle 1",
        "city":"Cali",
        "state":"Valle",
        "country":"Colombia",
        "PractitionerName" : "Dr. Evan94 Bins636",
        "PractitionerId" : "47ea5939-fff5-439a-9668-f3c3974b4a88"
        }

Response = requests.post(Url, headers=Headers, json=data)
return Response.json()
```
La respuesta puede ser.
```python
#Respuesta valida
{"Status":"True"}

#Respuesta no valida por un error inesperado
{'Error en PostPatients': TypeError}
```
##### (POST) PostAppointment.

En este llamado el cliente le pide a la API que cree un nuevo appointment, los headers para el llamado son los siguientes:
```python
{
    'Action' : 'PostAppointment',
    'Content-Type': 'application/json',
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
}
```
El json de este llamado es de la siguiente mandera:
```python
{
    'Type' : 'Revicion por enfermedad desconicida',
    'PatientId' : 'Patient/33d380ba-004a-4c52-bdbc-dbe0d2f68dce',
    'PractitionerId' : 'Practitioner/47ea5939-fff5-439a-9668-f3c3974b4a88',
    'PatientName' : 'Mr. Nickolas58 Flatley871',
    'PractitionerName' : 'Dr. Evan94 Bins636',
    'PeriodStart' : '2023-09-27T13:02:01-04:00'
    'PeriodEnd' : '2023-09-27T14:02:01-04:00'
}
```
Ejemplo:
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers = {
        'Action' : 'PostAppointment',
        'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
        'Content-Type': 'application/json'
        }

data = {
        'Type' : 'Revicion por enfermedad desconicida',
        'PatientId' : 'Patient/33d380ba-004a-4c52-bdbc-dbe0d2f68dce',
        'PractitionerId' : 'Practitioner/26g5gd654dfg5df6',
        'PatientName' : 'Mr. Nickolas58 Flatley871',
        'PractitionerName' : 'Dr. Evan94 Bins636',
        'Period' : '2023-09-27T13:02:01-04:00'
        }

Response = requests.post(Url, headers=Headers, json=data)
return Response.json()
```

La respuesta puede ser.
```python
#Respuesta valida
{"Status":"True", "Apointment": Apointment}

#Respuesta no valida por un error inesperado
{'Error en PostAppointment': TypeError}
```
##### (POST) Delete

En este llamado el cliente le pide a la API que elimine todos los recursos de FHIR que correspondan al tipo de recurso enviado en el header "Resource", la estructura de los header es de la siguiente manera:
```python
{
    'Action' : Delete,
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
    'Resource' : 'Patient'
}
```
El hearder Resource puede tener alguno de los siguientes 4 valores:

1. **Patient**: elimina todos los pacientes de Fhir pero no la relación con el médico

1. **CareTeam**: elimina todos los equipos de Fhir, este recurso DEBE SER BORRADO al eliminar todos los pacientes

1. **Encounter**: elimina todas las citas medicas que hay guardadas en Fhir

1. **Practitioner**: elimina todos los médicos guardados en Fhir

Ejemplo:
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : 'Delete',
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
            'Resource': 'Patient'
            }

Response = requests.post(Url, headers=Headers)
return Response.json()
```

La respuesta puede ser.
```python
#Respuesta valida
{"Status":"True"}

#Respuesta no valida por un error inesperado
{"Status":"False","Error":"Tipo de recurso no encontrado: Resource"}
```
##### (POST) DataFrondEnd

En este llamado el cliente le pide a la API que envie el archivo comprimido del paciente solicitado, la estructura de los header es de la siguiente manera:
```python
{
    'Action' : 'DataFrontEnd',
    'Clave': '4|484D0$343|84CK3ND',
    'Content-Type': 'application/json',
    'PatientId' : '24ef6aae-d847-48ce-980d-0e73fdafe414'#CAMBIAR
}
```
Ejemplo:
```python
import requests
import shutil
import os
import zipfile

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =  {
            'Action' : 'DataFrontEnd',
            'Clave': '4|484D0$343|84CK3ND',
            'Content-Type': 'application/json',
            'PatientId' : '24ef6aae-d847-48ce-980d-0e73fdafe414'#CAMBIAR
            }

Response = requests.post(Url, headers=Headers)
print("Data descargada")

if os.path.exists("DataF"):
    shutil.rmtree("DataF")

try:
    ZipData = Response.json()
    FilenName = ZipData['FileName']
    ZipData64 = ZipData['File']
    # Decodifica el binario desde base64
    ZipData64 = base64.b64decode(ZipData64)
    withopen(FilenName, 'wb') as DataFile:
        DataFile.write(ZipData64)

except Exceptionas e:
    print(e)

print("decomprimiendo...")
with zipfile.ZipFile(FilenName, 'r') as zip\_ref:
    zip\_ref.extractall("DataF")

os.remove(FilenName)
print("Data descomprimida")
```

La respuesta es el archivo solicitado
##### (POST) DeleteUser

En este llamado el cliente le pide a la API que elimine del servidor todos los archivos relacionados a un paciente dado su id, la estructura de los header es de la siguiente manera:
```python
{
    'Action' : 'DeleteUser',
    'Token': 'ajsdpajiopdj',
    'PatientId': PatientId
}
```
Ejemplo:
```python
import requests
import random

url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : 'DeleteUser',
            'Token': 'asdasdsa',
            'PatientId': PatientId
            }

Response = requests.post(url, headers=Headers)
return Response.json()
```

La respuesta puede ser
```python
#Respuesta valida
{"Status":"True"}

#Respuesta no valida por un error inesperado
{'Error en DeleteUser': TypeError}
```
##### (POST) GetInactivePatients

En este llamado el cliente le pide a la API le retorne los pacientes que están inactivos, la estructura de los header es de la siguiente manera:
```python
{
'Action' : 'GetInactivePatients',
'PractitionerId' : '7c2689b9-53da-44d9-b012-4ebdd1c1dbe2',
'Token':sdadasdasdads
}
```

Ejemplo:
```python
import requests
import random

url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : 'GetInactivePatients',
            'Token': 'asdasdsa',
            'PractitionerId' : '7c2689b9-53da-44d9-b012-4ebdd1c1dbe2'
           }

Response = requests.post(url, headers=Headers)
return Response.json()
```

La respuesta puede ser

- Respuesta valida del servidor <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/JSON-GetInactivePatients.txt)

- Respuesta invalida del servidor
    ```python
    {'Error en GetPatients': str(e)}
    ```
##### (POST) UpdatePatient.

En este llamado el cliente le pide a la API que actualice a un paciente, los headers para el llamado son los siguientes:
```python
{
    'Action' : 'UpdatePatient',
    'Content-Type': 'application/json',
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
}
```
El json de este llamado es de la siguiente mandera:
```python
{
    "PatientId":31j3m12km3l12klj3kl13,
    "name":"Juan",
    "family":"Perez Perez",
    "phone":"1234567890",
    "gender":"male",
    "birthDate":"2021-09-09",
    "address":"Calle 1",
    "city":"Cali",
    "state":"Valle",
    "country":"Colombia"
    "PractitionerName" : "Dr. Evan94 Bins636",
    "PractitionerId" : "47ea5939-fff5-439a-9668-f3c3974b4a88"
}
```
Ejemplo:

```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : 'PostPatient',
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
            'Content-Type': 'application/json'
            }

data = {
        "PatientId":31j3m12km3l12klj3kl13,
        "name":"Juan",
        "family":"Perez Perez",
        "phone":"1234567890",
        "gender":"male",
        "birthDate":"2021-09-09",
        "address":"Calle 1",
        "city":"Cali",
        "state":"Valle",
        "country":"Colombia",
        "PractitionerName" : "Dr. Evan94 Bins636",
        "PractitionerId" : "47ea5939-fff5-439a-9668-f3c3974b4a88"
        }

Response = requests.post(Url, headers=Headers, json=data)
return Response.json()
```

La respuesta puede ser.
```python
#Respuesta valida
{"Status":"True", "Patient": PaitentInfo}

#Respuesta no valida por un error inesperado
{'Error en UpdatePatients': TypeError}
```
##### (POST) UpdateAppointment.

En este llamado el cliente le pide a la API que actualice un appointment, los headers para el llamado son los siguientes:
```python
{
    'Action' : 'UpdateAppointment',
    'Content-Type': 'application/json',
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
}
```
El json de este llamado es de la siguiente mandera:
```py
{
    'ApointmentId' : 'd978asd978ada8sd08a90d',
    'Type' : 'Revicion por enfermedad desconicida',
    'PatientId' : 'Patient/33d380ba-004a-4c52-bdbc-dbe0d2f68dce',
    'PractitionerId' : 'Practitioner/47ea5939-fff5-439a-9668-f3c3974b4a88',
    'PatientName' : 'Mr. Nickolas58 Flatley871',
    'PractitionerName' : 'Dr. Evan94 Bins636',
    'PeriodStart' : '2023-09-27T13:02:01-04:00'
    'PeriodEnd' : '2023-09-27T14:02:01-04:00'
}
```
Ejemplo:
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : 'PostAppointment',
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
            'Content-Type': 'application/json'
            }

data = {
        'Type' : 'Revicion por enfermedad desconicida',
        'PatientId' : 'Patient/33d380ba-004a-4c52-bdbc-dbe0d2f68dce',
        'PractitionerId' : 'Practitioner/26g5gd654dfg5df6',
        'PatientName' : 'Mr. Nickolas58 Flatley871',
        'PractitionerName' : 'Dr. Evan94 Bins636',
        'Period' : '2023-09-27T13:02:01-04:00'
        }

Response = requests.post(Url, headers=Headers, json=data)
return Response.json()
```
La respuesta puede ser.
```py
#Respuesta valida
{"Status":"True", "Apointment": Apointment}

#Respuesta no valida por un error inesperado
{'Error en PostAppointment': TypeError}
```
##### (POST) DeleteAppointment

En este llamado el cliente le pide a la API que elimine el appointmet de FHIR que correspondan al id de recurso enviado en el header "AppointmentId", la estructura de los header es de la siguiente manera:
```py
{
    'Action' : DeleteAppointment,
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
    'AppointmentId' : asd897a9s7d9asd
}
```
Ejemplo:
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : DeleteAppointment,
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
            'AppointmentId' : asd897a9s7d9asd
            }

Response = requests.post(Url, headers=Headers)
return Response.json()
```
La respuesta puede ser.

```py
#Respuesta valida
{"Status":"True"}

#Respuesta no valida por un recurso incorrecto
{"Status":"False","Error":"Tipo de recurso no encontrado: Resource"}

#Respuesta no valida por un recurso no encontrado
{"Status":"False","Error":f"Id no encontrado: {id}"}
```
##### (POST) InactivatePatient

En este llamado el cliente le pide a la API que inactive al paciente de FHIR que correspondan al id de recurso enviado en el header 'PatientId', la estructura de los header es de la siguiente manera:
```py
{
    'Action' : InactivatePatient,
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
    'PatientId' : asd897a9s7d9asd
}
```
Ejemplo:
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : InactivatePatient,
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
            'PatientId': asd897a9s7d9asd
            }

Response = requests.post(Url, headers=Headers)
return Response.json()
```
La respuesta puede ser.
```py
#Respuesta valida
{"Status":"True"}

#Respuesta no valida por un recurso incorrecto
{"Status":"False","Error":"Tipo de recurso no encontrado: Resource"}

#Respuesta no valida por un recurso no encontrado
{"Status":"False","Error":f"Id no encontrado: {id}"}
```
##### (POST) ActivatePatient

En este llamado el cliente le pide a la API que active al paciente de FHIR que correspondan al id de recurso enviado en el header 'PatientId', la estructura de los header es de la siguiente manera:
```py
{
    'Action' : ActivarePatient,
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
    'PatientId' : asd897a9s7d9asd
}
```
Ejemplo:
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : ActivarePatient,
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
            'PatientId': asd897a9s7d9asd
            }

Response = requests.post(Url, headers=Headers)
return Response.json()
```

La respuesta puede ser.
```py
#Respuesta valida
{"Status":"True"}

#Respuesta no valida por un recurso incorrecto
{"Status":"False","Error":"Tipo de recurso no encontrado: Resource"}

#Respuesta no valida por un recurso no encontrado
{"Status":"False","Error":f"Id no encontrado: {id}"}
```
##### (POST) GetPractitionerPhoto.

En este llamado el cliente le pide a la API que le retorne la foto de un medico, esta se retona en BINARIO dentro de un json, los headers para el llamado son los siguientes:
```py
{
    'Action' : 'GetPractitionerPhoto',
    'PractitionerId' : '9876543210',
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
}
```
Ejemplo:
```python
import requests
import base64

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Headers =   {
            'Action' : 'GetPractitionerPhoto',
            'PractirionerId' : '0123456789',
            'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e'
            }

Response = requests.post(Url, headers=Headers)
PhotoData = Response.json()
FilenName = PhotoData['FileName']
PhotoData64 = PhotoData['File']
# Decodifica el binario desde base64
PhotoData64 = base64.b64decode(PhotoData64)

withopen(FilenName, 'wb') as PhotoFile:
     PhotoFile.write(PhotoData64)
```
La respuesta puede ser.

-   Respuesta valida del servidor <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/JSON-AudioSummary.txt)

-   Respuesta no valida del servidor
    ```py
    {'Error en GetPractitionerPhoto': TypeError}
    ```
##### (POST) UpdatePractitionerPhoto.

En este llamado el cliente le envia a la API la foto del medico, los headers para el llamado son los siguientes:
```py
{
    'Action' : UpdatePractitionerPhoto,
    'Token' : 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e',
    'PractitionerId' : '9876543210',
}
```
Ejemplo
```python
import requests

Url ="https://doctorhelperaibackend-qa.azurewebsites.net"

Archivo ="Photo.jpg"
Files = {f'Photo': (f'{Archivo}', open(f'{Archivo}', 'rb'), f'image/{Archivo.split(".")[1]}')}

Headers =   {
            'Action' : "UpdatePractitionerPhoto",
            'Token': Toasdasdasdasdasdasdkeasdafn,
            "PractitionerId" : PractitionerId
            }

Response = requests.post(Url, files=Files, headers=Headers)
return Response.json()
```

La respuesta puede ser.
```py
#Respuesta valida
{"Status": "True"}

#Respuesta no valida por error desconocido
{'Error en UpdatePractitionerPhoto': TypeError}
```
##### (POST) WakeUpWord (Nuevo).

En este llamado el cliente le envia a la API archivos de audio en formato m4a correspondientes al comando dicho por el medico. estos dentro del archivo **DHAIWakeUpWord**. los headers para el llamado son los siguientes:
```python
{	
    'Action' : 'WakeUpWord', 
    'View': Vista,
    'Token':"2343423423523m4523k4h23",
    'PractitionerId':"1234567890"
}
```
El valor del header **View** corresponde a la vista desde la cual el medico esta enviando el comando, las vistas validas con su respectiva imagen son:


| Main | NewPatient | ListPatients |
|:----: | :----: | :----: |
| <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/Main.png" height="400" />| <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/NewPatient.png" height="400" />| <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/ListPatients.png" height="400" />|

| NewAppointments | Appointment | ListAppointments |
| :----: | :----: | :----: |
| <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/NewAppointments.png" height="400" />| <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/Appointment.png" height="400" />| <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/ListAppointments.png" height="400" />|

| RedyToRecord | Recording | Pause |
| :----: | :----: | :----: |
| <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/RedyToRecord.png" height="400" />| <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/Recording.png" height="400" />| <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/Pause.png" height="400" />|

| Finish |
| :------: |
| <img src="https://doctorhelperaibackend.blob.core.windows.net/documentation/Finish.png" height="400" /> |


Ejemplo
```python
import requests

Url = "https://doctorhelperaibackend-qa.azurewebsites.net" 

Files = {'DHAIWakeUpWord': (f'{PractitionerId}-WakeUpWord.m4a', open(f'ArchivosDePrueba/WUW/{Command}', 'rb'), 'audio/mp4')}
Headers = 	{	
            'Action' : 'WakeUpWord', 
            'View': Vista,
            'Token':"2343423423523m4523k4h23",
            'PractitionerId':"1234567890"
            }
Response = requests.post(Url, files=Files, headers=Headers)
return Response.json()
```
La respuesta puede ser.
```python
#Respuesta valida
{"Time": "465.16"}

#Respuesta no valida por error desconocido
{'Error en Planning': TypeError}
```
## Errores extra
En esta sección se verán los 3 tipos de errores extras que pueden salir al usar la API


### Error de token

Este error significa que el token entregado para las consultas no es válido, el formato de este error es:
```py
{'Error': 'Token not valid'}
```

### Error de Action

Este error significa que la acción entregada retorno un error desconocido, el formato de este error es:
```py
{'Error en Action': TypeError}
```
### Error al encontrar Action

Este error significa que la acción entregada no es válida, el formato de este error es:
```py
{'Error': 'Action not found: {Action}'}
```
