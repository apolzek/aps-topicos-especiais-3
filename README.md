**Nome**:
Topicos Especiais III - Trabalho I - Google Cloud


**Descrição**:
Joãozinho está fazendo uma coleção de dados de todos os carros que ele já viu. Para guardar esses dados ele quer salvar no datastore/firestore os carros com seus atributos placa, cor, preço, modelo e marca para isso você deverá implementar uma google cloud function, em qualquer linguagem que seja possível, que receba uma requisição http com os atributos mencionados e salve no datastore. Faça uma outra function que seja capaz de mostrar os dados de um veículo recebendo a placa como parâmetro. O trabalho deve ser comitado no git (github, gitlab) deve ter um readme.md mostrando o endpoint que deve ser usado para enviar os dados e para ler os dados, bem como um print do resultado da chamada da function, um mostrando o dado persistido no DataStore e outro print demonstrando a function que busca os dados. Pode ser em trio.

---

**Função 1**

* Nome: funcAddCar
* Linguagem: Python 3.8
* Endpoint: https://us-central1-labfree.cloudfunctions.net/aps-topicos-especiais/funcAddCar
* Tipo: POST
* Json:

```
{
    "cor": "Amarelo",
    "marca": "Volkswagen",
    "modelo": "Fusca",
    "placa": "HUL-31221",
    "preco": "9.500"
}
```
* Curl:
```
curl --location --request POST 'https://us-central1-labfree.cloudfunctions.net/aps-topicos-especiais/funcAddCar' \
--header 'Content-Type: application/json' \
--data-raw '{
    "cor": "Amarelo",
    "marca": "Volkswagen",
    "modelo": "Fusca",
    "placa": "HUL-31221",
    "preco": "9.500"
}'
```
* Código:
```
from google.cloud import firestore
from flask import jsonify

def funcAddCar(request):
    request_json = request.get_json()
    
    try:
        placa = request_json['placa']
        cor = request_json['cor']
        preco = request_json['preco']
        modelo = request_json['modelo']
        marca = request_json['marca']
    except:
        return 'Failed', 400

    db = firestore.Client()
    car_ref = db.collection('carros').document(placa)
    car = car_ref.get()

    if car.exists:
        return 'Failed', 409
    else:
        car_ref.set({
            'placa': placa,
            'cor': cor,
            'preco': preco,
            'modelo': modelo,
            'marca': marca
        })
        return 'Success', 200
```

![image1](https://i.imgur.com/oFySDL4.png)

* Firestore:

![image2](https://i.imgur.com/dpHSBvI.png)

**Função 2**

* Nome: funcAddCar
* Linguagem: Python 3.8
* Endpoint: https://us-central1-labfree.cloudfunctions.net/aps-topicos-especiais2/funcGetCar
* Tipo: POST
* Json:
```
{
    "placa":"HUL-31221"
}
```
* Código:
```
from google.cloud import firestore
from flask import jsonify

def funcGetCar(request):
    request_json = request.get_json()

    try:
        placa = request_json['placa']
    except:
        return 'Failed', 400
    
    db = firestore.Client()
    car_ref = db.collection('carros').document(placa)
    car = car_ref.get()

    if car.exists:
        return jsonify(car.to_dict()), 200
    else:
        return ' Not found.', 404
  ```
  
  ![image3](https://i.imgur.com/qBHewZv.png)
