# {{cookiecutter.app_name}}


{{cookiecutter.project_short_description}}

---

# Oficina Python Nordeste 2020-2021

Seja muito bem vindo a oficina **"O que é o que é? Aiohttp!"**

Nessa oficina vamos discutir abordagens assíncronas de programação e olhar mais de perto duas bibliotecas: Uma padrão do Python [asyncio](https://docs.python.org/3.6/library/asyncio.html) e outra que é construída sobre o `asyncio` [aiohttp](https://docs.aiohttp.org/en/stable/)

Também vamos criar um app que chama APIs externas e que faz ligações para o seu celular, direto do servidor.


## 1: Instalando o ambiente

Antes de começar, vamos confirmar se tudo que precisaremos está instalado e funcionando.

O seguinte script confirma se você tem os pacotes básicos necessários e se sua versão do Python é >3.5:

```python
python3 setup/confirm_environment.py
```

Se tudo correr bem, você verá:

```
Success! Your local environment is setup correctly and you're ready for this workshop!
```

Agora vamos criar um *virtual environment* para nosso app. Tudo bem se você quiser usar outras ferramentas como pyenv, virtualenv-wrapper, etc.

```bash
virtualenv venv
source venv/bin/activate
pip3 install -r requirements.txt
```



## 2: Criando as Contas

### Instalando ngrok

1. Abra um novo terminal
2. Visite [ngrok](https://ngrok.com)
3. Click `Get started for free`
4. Crie uma conta, se ainda não tiver.
5. Faça o download para a versão adequada do seu SO.
6. Siga as instruções de instalação para seu SO.
7. Execute o comando`./ngrok http 8080`
8. Copie o *forwarding domain* que ngrok gerou. Se quiser confirmar se está tudo certo, basta acessar o endereço que você copiou via browser e verás: `502 Bad Gateway` no terminal do ngork
9. Mantenha o terminal do ngork aberto

> Você vai ver  o erro 502  por que o app não está rodando na porta 8080 ainda.

### Conta Telnyx

1. Visite [Telnyx](https://portal.telnyx.com)
2. Faça login com a conta fornecida para a Oficina
3. Visite o menu lateral  [Call Controll](https://portal.telnyx.com/#/app/call-control/applications)
4. Edite a conexão que já existe para seu usuário.
5. Copie o id da conexão `ID` em algum lugar - vamos usar em breve.
6. Atualize o `Webhook URL` com a URL gerada pelo ngrok

> Adicione  /webhook no final da URL pois é nesse endpoint que nosso app vai receber Webhooks.

7. Navegue para o menu lateral API Keys
8. Crie uma nova API Key e copie para algum lugar - usaremos no nosso app.
9. Visite `Numbers` menu
10. Você já tem um número em sua conta, copie para usarmos em nosso app.



## 3: Vamos explorar o Asyncio

Vamos começar por esse script de exemplo.

```python
cd setup/
python3 asyncio_example.py
```

Abra `setup/asyncio_example.py` no seu editor e vamos entender o código.
Parece (e é) bem simples, mas esse script nos ensina bastante sobre execução assíncrona de programas.
Vamos olhar  `main()` e `__main__` mais de perto.

### \_\_main\_\_

A primeira coisa que estamos fazendo é pegar o `event loop `. Em seguida pedimos ao asyncio que rode `main()` no `event loop`  até que a corrotina se complete. Note que não estamos usando `await` pois `run_until_complete` recebe uma corrotina como argumento.

### main()

Primeiro estamos apenas imprimindo na tela a corrotina `coro_test()` sem usar `await`.  Isso vai te retornar algo como:

```bash
<coroutine object coro_test at 0x7fac4ef4f410>
```
e o método **não será executado**.

Na próxima instrução, quando usamos `await` aí sim o método `coro_test()` vai ser executado e imprime na tela:

```bash
this is a coroutine
```

Por fim, estamos usando ` await asyncio.gather()`. Esse método recebe corrotinas como argumento e as executa assíncronamente até que todas terminem. Só então a execução continua. Análogo ao `run_until_complete()` o método `gather` também `await` magicamente para você.

No nosso caso, estamos chamando 4 "heavy" tasks". Cada uma é apenas um `await asyncio.sleep(X)`. Embora tenhamos vários "sleeps", o script se encerra após 7 segundos que é o "sleep" mais longo (e não 7+2+3+1=13 segundos)

Isso porque quando a primeira "heavy" tasks" dorme, ela devolve o controle de volta ao `event loop` que procede para a próxima tarefa e assim por diante.

Assim que `heavy_task_4` termina seu trabalho (de dormir zZZz), ela faz um *call back* para o `event loop` devolvendo o controle para finalizar a próxima tarefa.



## 4: Próxima parada: Aiohttp

Vamos olhar para Aiohttp agora. Vamos começar pelo "cliente"

Abra o programa `aiohttp_example.py`  e vamos olhar o código. Neste programa estamos abrindo 4 websites assíncronamente, escrevendo o HTML dos sites na saída de log e o tempo que o cliente levou para completar a requisição.

Execute o programa e veja isso acontecendo:

```python
python3 aiohttp_example.py
```

Análogamente ao exemplo do `asyncio`, devemos notar nesse exemplo que o tempo total de execução do script é menor do que a soma do tempo dos requests individuais. Isso porque enquanto esperamos a resposta do servidor remoto, ao invés de bloquear o I/O, graças a utilização de corrotinas  e `asyncio.gather` o controle sobre o programa é devolvido ao `event loop` e o mesmo executa a próxima tarefa pronta para ser executada, até que todas as tarefas acabem.



## 5: **Dial-A-Joke**

Agora vamos falar sobre o lado do servidor Aiohttp. Nosso cookiecutter template já nos traz bastante funcionalidades prontas para agilizar nossa oficina. De fato, tudo que você precisa já está implementado!

Mas vamos para uma breve caminhada pela estrutura do código.

### Estrutura dos arquivos

#### dialajoke

É a raiz do nosso app - namespace

#### server

Onde as instalações e as configurações do nosso servidor se encontram. Ex: porta do servidor, CORS e onde declaramos as rotas e os handlers.

#### templates

Onde se encontram os HTML servidos pela aplicação. Para esse app temos uma página HTML que permite os usuários agendarem as ligações telefônicas.

#### config.dev.json

Arquivo de configuração onde armazenamos as variáveis estáticas do projeto

#### tests

Testes unitários e de integração.

#### Makefile

Facilitador para rodar o app e outras funcionalidades

### Funcionalidades

Toda a lógica desse app está programada dentro do `infrastructure`.

`server` - Onde se encontram todos os HTTP handlers e configuração do servidor, middlewares e etc.
`call_control.py` -  classe `CallControl`  que lida com os webhooks e dispara as ligações telefônicas.
`scheduler.py` - Armazena a classe `UnifiedTimedQueue` que controla a agenda de ligações e dispara as ligações no tempo correto - em outras palavras, uma fila chique.
`usecases.py` - Lógica de extração dos parâmetros.
`validators.py` -  Lógica de validação das requisições

### Próximos Passos: Processar os webhooks em segundo plano

Por que fazer isso?

Na vida real seu programa muito provavelmente vai querer processar mais que apenas o webhook. Talvez o programa precise acessar algum dado no banco de dados ou fazer uma chamada à uma API externa.

Essas operações podem levar algum tempo para serem concluídas. Se o programa demorar muito para responder ao webhook, talvez isso seja reenviado e pode causar algum comportamento inesperado, por exemplo.

Para resolver esse problema, podemos processar essas operações em uma tarefa em segundo plano.

Como Aiohttp é construído no topo do Asyncio, podemos usar sua API para fazer isto e permitir que o programa continue processando outras instruções sem ser bloqueado por operações lentas de I/O.

> Veja a doc do Asyncio!
>
> [https://docs.python.org/3.6/library/asyncio-eventloop.html#asyncio.AbstractEventLoop.create_task](https://docs.python.org/3.6/library/asyncio-eventloop.html#asyncio.AbstractEventLoop.create_task)



### Próximos Passos: Criar um middleware

Por que precisamos disso?

Além dos vários middlewares que já existem prontos por ai, pode existir alguma situação em que você precise criar o seu próprio middleware.

Um middleware customizado pode trazer várias vantagens:

1.  Um único ponto de coleta de métricas para todas as rotas. Isso ajuda a manter o seu código DRY.

2.  Captura de exceções no código para tratamento apropriado. Isso pode ajudar a manter um padrão de resposta das rotas em caso de exceções .

3.  Possibilita a criação de código reutilizável e plugável em diferentes aplicações

Vamos adicionar um middleware para nosso app? Precisa de ideias?

- Você pode logar o tempo que um request leva para ser processado
- Você pode capturar exceções e formatar o corpo de resposta (response)



> Veja a doc do Aiohttp!
>
> [https://docs.aiohttp.org/en/stable/web_advanced.html#middlewares](https://docs.aiohttp.org/en/stable/web_advanced.html#middlewares)



### Próximos Passos: Escreva testes

Por que isso é importante?

Você provavelmente deve imaginar, mas testes são extremamente importantes para garantir a qualidade da sua app.

Com testes você pode encontrar erros ou *bugs* antes de enviar seu código para o ambiente de produção. Outra grande vantagem dos testes é que você como dev tem mais segurança ao fazer mudanças importantes na base de código.

Nosso projeto já conta com um um caso de teste com exemplo.

tente:
```bash
make teste
```

Vamos ampliar a cobertura de testes?

> Veja o que a doc do Aiohttp sugere
>
> [https://aiohttp.readthedocs.io/en/stable/testing.html#pytest-example](https://aiohttp.readthedocs.io/en/stable/testing.html#pytest-example)



Esse projeto foi criado a partir de: [Python Nordeste Cookiecutter Template](https://github.com/luizanao/pyne2021-cookiecutter)
