1- ***Crie uma conta no Deta***
https://web.deta.sh/

2- **Instale a CLI do Deta no seu sistema com o comando a seguir:**
***Windows:***
> iwr https://get.deta.dev/cli.ps1 -useb | iex

***Mac ou Linux:***
> curl -fsSL https://get.deta.dev/cli.sh | sh

3- ***Depois de instalar a CLI, logue seu computador no Deta***
> deta login

4- ***Se deu tudo certo no seu login, você já pode criar seu micro com o seguinte comando***
> deta new --python meu_micro

5- ***A resposta é algo assim e tem que ter criado uma pasta no seu pc***

    {
    "name": "meu_micro",
    "runtime": "python3.7",
    "endpoint": "https://<path>.deta.dev",
    "visor": "enabled",
    "http_auth": "enabled"
    }

  

6- ***Para gerenciar as depêndencias ,acesse a pasta <meu_micro> e crie um arquivo requirements.txt parecido com esse:***

    flask
    deta

7- ***Agora edite o arquivo main.py para esse código***

    from flask import Flask
    
    app = Flask(__name__)
    
    @app.route('/', methods=["GET"])
    def hello_world():
	    return "Hello World"



8- ***Então use a CLI para fazer Deploy***
> deta deploy

9- ***Depois acesse o endpoint que foi retornado quando vc criou o micro***
> https://< path >.deta.dev"

10-***Agora que você já tem uma API no ar, crie um arquivo chamado detabase.py com esse código:***

    #Importe o Deta para se conectar com o servidor
    from deta import  Deta
    
    #Inicialize seu projeto no deta com a sua project key (Essa não é a melhor maneira de fazer isso)
    deta = Deta('<sua_project_key>')
    
    #Crie sua DetaBase dentro do Base do seu Projeto
    approved_payments = deta.Base("pagamentos_aprovados")

11- ***Crie sua DetaBase com o seguinte comando***
> python3 detabase.py

12- ***Agora crie um arquivo services.py que terão os serviços dos endpoints***

	#Importe as DetaBases que for usar
    from detabase import  approved_payments
    
    #Crie um serviço que te auxilie em adicionar os dados na DetaBase ao consumir o webhook
    def  create_approved_payment(payment_data):
	    return  approved_payments.put({
		    "0 - fullname": payment_data['Customer']['full_name'],
		    "1 - email": payment_data['Customer']['email'],
		    "2 - mobile": payment_data['Customer']['mobile'],
		    "3 - start_at": str(payment_data['Subscription']['start_date'])[0:10],
		    "4 - finish_at": str(payment_data['Subscription']['next_payment'])[0:10],
		    "5 - commission": payment_data['Commissions']['my_commission']
	    })

13- ***Edite o código do arquivo main.py novamente para algo parecido com isso:***

    #Importe o Flask e o módulo request
    from flask import Flask, request
    
    #Importe o serviço que acabou de criar
    from services import create_approved_payment
    
    #Defina o FlaskApp
    app = Flask(__name__)
    
    #Crie uma rota para receber o webhook
    @app.route('/approved_payment', methods=["POST"])
    
    #Determina o que vai acontecer quando tiver requisição post nesse endpoint
    def approved_payment():
	    return create_approved_payment(request.json)


14- ***Faça Deploy de novo***
> deta deploy

15- ***Teste seu Webhook No site da Kiwify!***