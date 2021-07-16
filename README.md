


![Python Nordeste 2021](https://raw.githubusercontent.com/pythonNordeste/pyne2020/master/images/banner.png?raw=true)
<h1 align="center">
    <br>
    <br>
    O que é O que é? Aiohttp!
<br>
</h1>

<h4 align="center">Oficina ministrada na <a href="http://electron.atom.io" target="_blank">Python Nordeste 2020-2021</a>.</h4>




Esse é um template gerador de projetos que usaremos para a oficina. Se quiser espiar o projeto antes de cria-lo, [você pode faze-lo aqui](base/{{cookiecutter.repo_name}})



## Instalação

Para criar o projeto a partir deste template, você precisa [instalar o cookiecutter](https://cookiecutter.readthedocs.io/en/latest/installation.html).

```bash
$ python3 -m pip install --user cookiecutter
```

Assim que estiver instalado, você estará pronto para [criar o projeto](https://cookiecutter.readthedocs.io/en/latest/usage.html#generate-your-project).



## Slides da Apresentação

[Em breve](luizanao.github.io)



## Como Usar:

Para gerar o projeto final, basta executar o comando:

```bash
$ cookiecutter base

Select event:
1 - Python Nordeste 2020-2021
Choose from 1 (1) [1]: 1
primary_maintainer []: Luiz
app_name [dialajoke]:
repo_name [dial-a-joke]:
container_name [dial-a-joke]:
project_short_description [Agendador de piadas da oficina da Python Nordeste 2020-2021]:
```

e um novo diretório será criado com o mesmo nome indicado no campo `app_name`.


## Parâmetros do Usuário

No momento da criação desse template, algumas perguntas serão feitas para o usuário. Pense com cuidado nas respostas pois esses parâmetros serão utilizado para a criação da estrutura do projeto.

| #                         | Escolhas                    | tipo   | O que significa?                       |
| ------------------------- | --------------------------- | ------ | --------------------------------------- |
| event                     | `Python Nordeste 2020-2021` | string | Apenas uma metadado para o Projeto      |
| primary_maintainer        | João                        | string | Nome do programador mantedor do projeto |
| app_name                  | dialajoke                   | string | Nome do app                             |
| repo_name                 | dial-a-joke                 | string | Nome do repositório do github           |
| container_name            | dial-a-joke                 | string | Nome do container Docker.              |
| project_short_description | --                          | string | breve descrição do app                  |
