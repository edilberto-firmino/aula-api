# Primeira API com Python e FastAPI

Material de apoio para a primeira aula de API. Vamos começar com uma resposta simples e depois construir um cadastro de produtos, usando um único arquivo Python.

**Ao terminar, você conseguirá:** iniciar uma API, testar suas rotas pelo navegador, cadastrar produtos e entender respostas de sucesso e de erro.

Você precisa de conhecimentos básicos de Python: funções, classes, listas e dicionários. Não precisa criar um frontend nem instalar um banco de dados nesta aula.

## 1. O que é uma API?

Uma API permite que programas se comuniquem. Por exemplo, um aplicativo de loja pede a lista de produtos para uma API, que responde com os dados.

O fluxo desta aula será:

```text
Navegador ou página /docs → requisição HTTP → API → resposta com dados em JSON
```

- **Requisição:** pedido enviado para a API.
- **Rota:** caminho que identifica uma operação, como `/produtos`.
- **Método HTTP:** indica a operação desejada, como consultar ou cadastrar.
- **Resposta:** dados e código de status devolvidos pela API.
- **JSON:** formato de texto usado para trocar dados.

Exemplo de JSON:

```json
{
  "nome": "Teclado",
  "preco": 120.0
}
```

Em JSON, as chaves e os textos usam aspas duplas. Números decimais usam ponto: `120.50`, e não `120,50`.

**FastAPI** é um framework Python para criar APIs. Ele também gera uma página interativa em `/docs`, que vamos usar para enviar requisições. Veja os [primeiros passos na documentação oficial](https://fastapi.tiangolo.com/tutorial/first-steps/).

## 2. Prepare o computador

Instale o Python pelo [site oficial](https://www.python.org/downloads/). Use Python 3.10 ou superior para acompanhar este material. No instalador do Windows, marque **Add Python to PATH**, se essa opção aparecer.

Você pode editar os arquivos no [Visual Studio Code](https://code.visualstudio.com/). Abra o terminal em **Terminal → Novo Terminal** e use o PowerShell no Windows.

Confira a instalação:

```powershell
python --version
```

Se o comando não funcionar, tente `py --version`. Nesse caso, substitua `python` por `py` nos comandos de criação do ambiente virtual.

> Os blocos de comandos devem ser digitados no terminal. Os blocos de Python devem ser escritos no arquivo `main.py`. Não copie as marcações de início e fim dos blocos.

## 3. Crie a pasta e o ambiente virtual

Se você já abriu a pasta desta aula no VS Code, use o terminal dessa pasta e pule os dois primeiros comandos. Para começar em uma nova pasta:

```powershell
mkdir minha-api
cd minha-api
```

Crie e ative o ambiente virtual:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

O ambiente virtual separa os pacotes deste projeto dos pacotes de outros projetos. Normalmente, aparece `(.venv)` no início da linha quando ele está ativo.

Se o PowerShell bloquear a ativação, você pode trabalhar sem ativar o ambiente, usando diretamente seus executáveis:

```powershell
.\.venv\Scripts\python.exe -m pip install "fastapi[standard]"
.\.venv\Scripts\fastapi.exe dev main.py
```

Execute o segundo comando somente depois de criar o `main.py`, na próxima etapa. Se usar essa alternativa, a instalação abaixo já terá sido feita.

No Linux/macOS, os comandos equivalentes são:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

## 4. Instale o FastAPI

Com o ambiente virtual ativo:

```powershell
python -m pip install "fastapi[standard]"
```

As aspas fazem parte do comando. Essa instalação inclui os componentes padrão necessários para executar o servidor de desenvolvimento. O uso de ambiente virtual com `pip` está descrito na [documentação oficial de instalação e ambientes virtuais](https://fastapi.tiangolo.com/virtual-environments/).

Ao longo da aula, sua pasta terá esta estrutura:

```text
minha-api/
├── .venv/     ← ambiente virtual; não edite seus arquivos
└── main.py    ← código da nossa API
```

## 5. Crie sua primeira rota

Crie o arquivo **`main.py`** na pasta do projeto, fora da `.venv`, e escreva:

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def inicio():
    return {"mensagem": "Minha primeira API está funcionando!"}
```

| Trecho | O que faz |
| --- | --- |
| `from fastapi import FastAPI` | Importa a classe que cria a aplicação. |
| `app = FastAPI()` | Cria a aplicação. |
| `@app.get("/")` | Associa a função abaixo a uma requisição GET na rota `/`. |
| `def inicio():` | Define a função executada ao acessar a rota. |
| `return {...}` | Devolve um dicionário, convertido em JSON pelo FastAPI. |

O `@app.get(...)` é um **decorador**: aqui, ele registra a função como uma operação da API. Para estes exemplos, podemos usar funções com `def`.

Salve o arquivo e execute no terminal, dentro da pasta que contém `main.py`:

```powershell
fastapi dev main.py
```

O comando inicia o servidor de desenvolvimento e recarrega a aplicação quando você salva alterações. Veja a [referência dos primeiros passos e da execução](https://fastapi.tiangolo.com/tutorial/first-steps/).

Abra no navegador: <http://127.0.0.1:8000/>

Resposta esperada:

```json
{
  "mensagem": "Minha primeira API está funcionando!"
}
```

`127.0.0.1` aponta para o seu próprio computador; `8000` é a porta usada pelo servidor. Deixe o terminal aberto. Para encerrar, pressione **Ctrl + C**.

**Confira antes de continuar:** a mensagem apareceu no navegador? Se apareceu, sua primeira rota está funcionando!

## 6. Conheça a página de testes

Abra <http://127.0.0.1:8000/docs>.

Essa página é a documentação interativa da API, também conhecida como Swagger UI. Abra **GET /**, clique em **Try it out** e depois em **Execute**.

Observe o campo **Server response**: ele mostra o código de status e o corpo da resposta. A seção **Responses** descreve as respostas possíveis; ela não é, necessariamente, o resultado da sua execução.

Digitar uma URL na barra do navegador envia uma consulta GET. Para cadastrar, atualizar e excluir, usaremos os botões do `/docs`.

## 7. Evolua para uma API de produtos

Agora vamos praticar **CRUD**: criar, consultar, atualizar e excluir dados.

| Método | Rota | Operação |
| --- | --- | --- |
| GET | `/` | Verificar a mensagem inicial. |
| GET | `/produtos` | Listar os produtos. |
| GET | `/produtos/{produto_id}` | Consultar um produto pelo ID. |
| POST | `/produtos` | Criar um produto. |
| PUT | `/produtos/{produto_id}` | Atualizar nome e preço de um produto. |
| DELETE | `/produtos/{produto_id}` | Excluir um produto. |

**Substitua todo o conteúdo de `main.py`** pelo exemplo completo abaixo. Não acrescente este código ao exemplo anterior.

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel, Field

app = FastAPI(title="Minha API de Produtos")


class ProdutoEntrada(BaseModel):
    nome: str = Field(min_length=1)
    preco: float = Field(gt=0)


# Os dados ficam apenas na memória durante a execução.
produtos = {}
proximo_id = 1


@app.get("/")
def inicio():
    return {"mensagem": "API de produtos funcionando!"}


@app.get("/produtos")
def listar_produtos():
    return list(produtos.values())


@app.get("/produtos/{produto_id}")
def buscar_produto(produto_id: int):
    if produto_id not in produtos:
        raise HTTPException(status_code=404, detail="Produto não encontrado")
    return produtos[produto_id]


@app.post("/produtos", status_code=201)
def criar_produto(produto: ProdutoEntrada):
    global proximo_id

    novo_produto = {
        "id": proximo_id,
        "nome": produto.nome,
        "preco": produto.preco,
    }
    produtos[proximo_id] = novo_produto
    proximo_id += 1
    return novo_produto


@app.put("/produtos/{produto_id}")
def atualizar_produto(produto_id: int, produto: ProdutoEntrada):
    if produto_id not in produtos:
        raise HTTPException(status_code=404, detail="Produto não encontrado")

    produtos[produto_id] = {
        "id": produto_id,
        "nome": produto.nome,
        "preco": produto.preco,
    }
    return produtos[produto_id]


@app.delete("/produtos/{produto_id}")
def excluir_produto(produto_id: int):
    if produto_id not in produtos:
        raise HTTPException(status_code=404, detail="Produto não encontrado")

    del produtos[produto_id]
    return {"mensagem": "Produto excluído com sucesso"}
```

Salve o arquivo. Se o servidor ainda estiver rodando, aguarde o recarregamento; caso contrário, execute `fastapi dev main.py`. Atualize a página `/docs` para ver as novas rotas.

### Entenda as novas partes

- `ProdutoEntrada` é uma classe que descreve os dados recebidos: nome e preço. Ela herda de `BaseModel`, do Pydantic, para validar esses dados.
- `nome: str` indica texto; `min_length=1` impede uma string vazia. Essa regra simples ainda aceita um nome formado apenas por espaços.
- `preco: float` indica um número; `gt=0` exige um valor maior que zero.
- `produto_id: int` faz o FastAPI validar o ID da URL como um número inteiro. Em `/produtos/1`, o ID recebido é `1`.
- `produto: ProdutoEntrada` recebe os dados JSON do **corpo da requisição**. O aluno não precisa enviar o ID no cadastro: a API o cria.
- `produtos` é um dicionário que relaciona cada ID aos dados do produto.
- `global proximo_id` permite atualizar o contador definido fora da função. É uma simplificação para esta aula.
- `HTTPException` interrompe a operação e devolve um erro HTTP. Usamos `404` quando o produto não existe.

Confira as referências oficiais sobre [dados no corpo da requisição](https://fastapi.tiangolo.com/tutorial/body/) e [tratamento de erros](https://fastapi.tiangolo.com/tutorial/handling-errors/).

> **Os cadastros são temporários.** Reiniciar o servidor, inclusive pelo recarregamento ao salvar o código, apaga todos os produtos e reinicia o contador. O arquivo `main.py` continua salvo. Este armazenamento serve para a prática local da aula; persistência em banco de dados será uma próxima etapa.

> Usamos `float` para simplificar o exercício. Sistemas financeiros precisam de um tratamento adequado para valores monetários, como `Decimal` ou centavos inteiros.

## 8. Teste o CRUD, passo a passo

Faça a sequência sem editar o código entre os testes, para evitar que os cadastros sejam apagados pelo recarregamento.

### A. Liste antes de cadastrar

No `/docs`, abra **GET /produtos → Try it out → Execute**. Você deve receber status `200` e uma lista vazia: `[]`.

### B. Cadastre um produto

Abra **POST /produtos**, clique em **Try it out**, substitua o conteúdo de **Request body** por este JSON e clique em **Execute**:

```json
{
  "nome": "Teclado",
  "preco": 120.0
}
```

Se este for o primeiro cadastro desde que o servidor iniciou, a resposta terá status `201` e será:

```json
{
  "id": 1,
  "nome": "Teclado",
  "preco": 120.0
}
```

Anote o ID retornado. Se você já cadastrou outros produtos, ele pode ser diferente de `1`.

### C. Consulte

Execute **GET /produtos** novamente: a lista deve conter o produto criado. Depois, execute **GET /produtos/{produto_id}**, preenchendo `produto_id` com o ID anotado.

### D. Atualize

Execute **PUT /produtos/{produto_id}** com o mesmo ID e este corpo:

```json
{
  "nome": "Teclado USB",
  "preco": 99.9
}
```

A resposta deve ter status `200` e mostrar o mesmo ID com os novos dados. Neste exemplo, PUT exige os dois campos: nome e preço.

### E. Exclua

Execute **DELETE /produtos/{produto_id}** com o mesmo ID. Você deve receber status `200` e:

```json
{
  "mensagem": "Produto excluído com sucesso"
}
```

Consulte o ID excluído usando GET. Agora, a resposta deve ter status `404`:

```json
{
  "detail": "Produto não encontrado"
}
```

### F. Teste as regras de validação

Tente cadastrar:

```json
{
  "nome": "Mouse",
  "preco": -10
}
```

A resposta deve ter status `422`, pois o preço precisa ser maior que zero. Tente também nome vazio (`""`), preço zero e ausência do campo `nome`. Esses dados também devem ser rejeitados.

| Status | Significado nesta aula |
| --- | --- |
| `200` | Operação realizada com sucesso. |
| `201` | Produto criado. |
| `404` | Produto não encontrado. |
| `422` | Dados enviados não atendem às regras. |

## 9. Atividade prática

1. Cadastre três produtos e anote seus IDs.
2. Liste todos os produtos e confira os dados.
3. Consulte um produto pelo ID.
4. Atualize nome e preço de um dos produtos.
5. Exclua outro e consulte seu ID para verificar o `404`.
6. Tente cadastrar um preço negativo e verifique o `422`.
7. Reinicie o servidor e confira que a lista voltou a ficar vazia.

**Registre os resultados:** para cada operação, anote método, rota, status e resposta. Você pode entregar essas anotações junto com o `main.py`.

Para discutir em sala: qual é a diferença entre a rota `/produtos` e `/produtos/1`? Por que nome e preço vão no corpo do POST, enquanto o ID vai na URL da busca? Por que os produtos desaparecem ao reiniciar?

**Desafio opcional:** adicione um campo `estoque`, inteiro e maior ou igual a zero, ao modelo. Inclua esse campo nos dicionários de criação e atualização. Teste um estoque válido e outro negativo.

## 10. Como retomar o projeto em outra aula

Abra a pasta do projeto no VS Code e um novo terminal. No Windows:

```powershell
.\.venv\Scripts\Activate.ps1
fastapi dev main.py
```

Se a ativação estiver bloqueada, use `.\.venv\Scripts\fastapi.exe dev main.py`.

Você não precisa reinstalar o FastAPI enquanto o ambiente virtual existir. Abra novamente <http://127.0.0.1:8000/docs>. Os produtos precisarão ser cadastrados outra vez.

## 11. Problemas comuns

| Problema | Como resolver |
| --- | --- |
| `python` não reconhecido | Tente `py` no Windows; confira a instalação e abra um novo terminal. |
| Ativação bloqueada no PowerShell | Use os executáveis da `.venv` diretamente, conforme a etapa 3. |
| `fastapi` não reconhecido | Ative a `.venv` e instale `"fastapi[standard]"`; ou use `.\.venv\Scripts\fastapi.exe`. |
| `No module named fastapi` | Instale o pacote com o Python da `.venv`. No VS Code, selecione esse ambiente em **Python: Select Interpreter**. |
| `main.py` não encontrado | Confira se o terminal está na pasta do arquivo e se ele não foi salvo como `main.py.txt`. |
| Erro de indentação | Preserve os recuos do exemplo; use quatro espaços dentro de classes, funções e condições. |
| Porta 8000 ocupada | Execute `fastapi dev main.py --port 8001` e abra `http://127.0.0.1:8001/docs`. |
| Navegador não conecta | Confira se o servidor está rodando e se a porta da URL é a mesma indicada no terminal. |
| Produto retorna `404` | Confira o ID; ele pode ter sido excluído ou os dados apagados por um reinício. |
| Resposta `422` | Confira os campos obrigatórios, os tipos e o preço maior que zero. |
| Novas rotas não aparecem no `/docs` | Salve o arquivo, aguarde o recarregamento e atualize a página. |

## Nota para o professor: revisão do material original

O material original oferece uma boa sequência inicial: ambiente virtual, primeira rota, execução local e testes pelo navegador. Porém, o exemplo apresentado como completo estava interrompido na busca por ID: faltava retornar o produto encontrado e implementar POST, PUT e DELETE. Assim, o aluno não conseguiria realizar o cadastro nem concluir o exercício proposto.

Este roteiro completa essas operações, organiza os códigos em blocos copiáveis e acrescenta uma sequência de testes com resultados esperados. Para a primeira aula, priorize as etapas 1 a 6; avance para o CRUD conforme o ritmo da turma. Banco de dados, autenticação e organização em múltiplos arquivos podem ficar para aulas seguintes.
#
