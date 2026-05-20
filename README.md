# Documentação da API REST
### 1.1) O que é uma API? Expliquem a sigla, pra que serve, por que é importante. Tentem usar uma analogia do dia a dia
API (Interface de programação de aplicativos) é um conjunto de regras e protocolos que permitem que diferentes sistemas conversem entre si. Interface: o ponto onde duas coisas se ligam e interagem, programação: é feita para códigos e sistemas utilizarem ela, Aplicativos: representa o software.

A API serve para conectar sistemas diferentes, funcionando como um tradutor ou mensageiro. Ele é importante pois permite que o programador utilizem os códigos que quiserem sem precisar modificar eles para o outro sistema entender.

A API funciona como um Caixa Eletrônico, o usuário não acessa o cofre ou o sistema interno do banco diretamente, ele interage apenas com a interface (botões e tela), que faz a ponte segura entre as duas partes. O usuário solicita o saque (Requisição), depois a máquina valida a operação (processamento) e por último, o dinheiro é entregue ou da erro (resposta).

### 1.2) O que é REST? Expliquem o que é uma API REST?
a REST (Transferência de Estado Representacional) funciona como um manual do que deve ou não ser feito para o compartilhamento de dados na WEB, ele veio para padronizar o protocolo de HTTP. Para ser considerado um REST, é preciso que a mensagem enviada tenha todas as informações necessárias para ser entendida, sem depender do que foi enviado antes.

a API REST é um conjunto de regras que dita como os sistemas devem conversar na internet, criando verbos do HTTP para padronizar as ações. GET: Para buscar informações, POST: Para enviar ou criar informações, PUT: Para atualizar dados já existentes e DELETE: Para apagar um registro.

### 1.3) O que é CRUD? Expliquem cada letra e relacionem cada operação com um método HTTP. Importante: no nosso projeto só implementamos o C (Create, via POST) e o R (Read, via GET). Expliquem também o que seriam o U (Update) e o D (Delete), e quais métodos HTTP fariam essas operações.

o CRUD (C - Create (Criar), R - Read (Ler), U - Update (Atualizar) e D - Delete (Apagar)) representa as 4 operações essenciais para o sistema realizar uma lista ou um banco de dados. No nosso projeto, utilizamos apenas o POST (criar) e o  GET (read)

C - Create (Criar / POST): Insere uma nova informação. É quando o ESP32 envia e cadastra uma nova leitura de temperatura no servidor.

R - Read (Ler / GET): Consulta os dados salvos. É o que usamos para listar o histórico de temperaturas no navegador ou no Postman.

U - Update (Atualizar / PUT ou PATCH): Altera um dado existente, como corrigir o valor de uma umidade que foi gravada errada.

D - Delete (Apagar / DELETE): Remove um registro do sistema permanentemente.

### 1.4) O que é HTTP e o que são status codes? Expliquem o protocolo de forma simples. Façam uma tabela com pelo menos 6 status codes incluindo obrigatoriamente 200, 201, 400, 404 e 500. 

o HTTP (Protocolo de Transferência de Hipertexto) é o protocolo da internet que permite a troca de dados e de conteúdos entre o navegador (cliente) e o hospedeiro (servidor). Os Status Codes (Códigos de Status) são números de três dígitos que o servidor envia de volta para dizer o que aconteceu com o pedido. 

| codigo | nome | significado | quando aparece |
| ------ | ---- | ----------- | -------------- | 
| 200 | ok | O pedido foi realizado com sucesso. | Quando fazemos um GET e a lista de temperaturas aparece corretamente. |
| 201 | created | Um novo recurso foi criado com sucesso. | Quando o POST funciona e salvamos uma nova leitura do ESP32. |
| 400 | Bad Request | O servidor não entendeu a requisição (erro de sintaxe). | Se tentarmos enviar um JSON mal formado ou faltando campos obrigatórios. |
| 401 | Unauthorized | O acesso foi negado por falta de autenticação. | Se tentarmos acessar a API sem uma chave de segurança (caso a gente crie uma). |
| 404 | Not Found |	O recurso ou rota não foi encontrado. |	Se digitarmos a URL errada, como localhost:3000/api/sensor-errado. |
| 500 | Internal Server Error | Ocorreu um erro inesperado no servidor. | Se o nosso código Node.js tiver um erro de lógica e o servidor "crashar". |

### 1.5) O que é JSON e por que usamos? Expliquem o formato, mostrem um exemplo de uma leitura nossa em JSON, e justifiquem por que esse formato é tão usado em APIs.
O JSON (Notação de Objetos JavaScript) é um formato leve de troca de dados entre computadores. onde cada item tem uma chave e um valor e foi criado para ser usado facilmente por humanos por ser em formato de texto. 

O principal motivo para utilizarmos é sua universalidade e simplicidade. Por ser simples e ser em texto, a maioria das linguagens podem entender o JSON, além de ser curto, então, consome pouca internet, sendo perfeito para o nosso ESP32 

{

        "id": 2,

        "temperatura": 20,

        "umidade": 70,

        "hora": "10:00"

    } 



##  DOCUMENTAÇÃO DOS ENDPOINTS/ROTAS
#### 📊 1. Buscar Histórico Completo
Rota: GET /api/dados

Descrição: Retorna a lista com todas as leituras de sensores armazenadas.

Parâmetros: Nenhum.

Exemplo de Requisição: GET http://localhost:3000/api/dados

Resposta de Sucesso (201 OK):

[
  {"id": 1, "temperatura": 30, "umidade": 40, "hora": "09:00"},
  {"id": 2, "temperatura": 25, "umidade": 56, "hora": "10:00"},
  {"id": 3, "temperatura": 20, "umidade": 30, "hora": "11:00"}
]

#### 🔍 2. Buscar por ID Específico
Rota: GET /api/dados/:id

Descrição: Filtra e retorna apenas o registro correspondente ao ID enviado na URL.

Parâmetros: id (obrigatório, na URL).

Exemplo de Requisição: GET http://localhost:3000/api/dados/2

Resposta de Sucesso (200 OK):

{"id": 2, "temperatura": 25, "umidade": 56, "hora": "10:00"}

Resposta de Erro (404 Not Found):
{"mensagem": "ID não encontrado!"}

#### 📥 3. Cadastrar Nova Leitura
Rota: POST /api/dados

Descrição: Recebe os dados do sensor e cria um novo registro com ID gerado automaticamente.

Parâmetros: Nenhum na URL. Requer corpo (Body) em JSON.

Exemplo de Requisição: POST http://localhost:3000/api/dados

Body:
{
  "temperatura": 28,
  "umidade": 45,
  "hora": "12:00"
}

Resposta de Sucesso (201 Created):

JSON
{
  "mensagem": "Dados enviados com sucesso!",
  "dados": {"id": 4, "temperatura": 28, "umidade": 45, "hora": "12:00"}
}

Resposta de Erro (400 Bad Request):

JSON
{"mensagem": "Dados incompletos! Verifique novamente!"}

#### 🔄 4. Atualizar Registro Existente
Rota: PUT /api/dados/:id

Descrição: Substitui as informações de um registro salvo com base no ID informado.

Parâmetros: id (obrigatório, na URL) e Corpo (Body) em JSON.

Exemplo de Requisição: PUT http://localhost:3000/api/dados/2

Body:

JSON
{
  "temperatura": 26,
  "umidade": 50,
  "hora": "10:30"
}

Resposta de Sucesso (200 OK):

JSON
{"mensagem": "Dados atualizados com sucesso!"}

Resposta de Erro (404 Not Found):

JSON
{"mensagem": "Não é possível atualizar. ID inexistente!"}

#### ❌ 5. Remover Registro do Histórico
   
Rota: DELETE /api/dados/:id

Descrição: Exclui permanentemente um registro do sistema utilizando o ID.

Parâmetros: id (obrigatório, na URL).

Exemplo de Requisição: DELETE http://localhost:3000/api/dados/3

Resposta de Sucesso (200 OK):

JSON
{"mensagem": "Dados excluídos com sucesso!"}
Resposta de Erro (404 Not Found):

JSON
{"mensagem": "Não é possível excluir um dado inexistente!"}
## DIAGRAMA DA ARQUITETURA
<img width="961" height="423" alt="image" src="https://github.com/user-attachments/assets/5913c00f-ca9d-48c4-9991-ad1fb8c7b3e5" />

📝 Legenda do Diagrama
ESP32 (Dispositivo IoT): Lê a temperatura e a umidade do ambiente.

1. POST /api/dados: O sensor envia as leituras em formato JSON para o servidor.

API Node.js + Express: Recebe o pedido, processa a informação e gerencia o sistema.

Banco de Dados (Em Memória): Um array na memória RAM que guarda todo o histórico de dados recebidos.

Postman (Cliente/Usuário): Ferramenta usada para testar e visualizar as informações.

2. GET /api/dados: O Postman solicita a lista com o histórico de leituras.

3. Resposta JSON (Status 200): O servidor busca os dados guardados e devolve para a tela do usuário com sucesso.

## COMO RODAR E REFLEXÃO

#### 4.1) Como Rodar o Projeto
1. Pré-requisitos:

Node.js instalado (versão 18 ou superior).

2. Como Instalar:

Abra o terminal na pasta do projeto e instale as dependências com o comando:

Bash
npm install
3. Como Rodar:

Para iniciar o servidor, execute:

Bash
node server.js

#### 4. Como saber se está funcionando:

O terminal mostrará a mensagem de confirmação:

Servidor rodando na porta 3000

#### 5. Como Testar (Via Postman):

Listar histórico (GET): Use o método GET na URL http://localhost:3000/api/dados para ver as leituras salvas.

Cadastrar dados (POST): Use o método POST na mesma URL. Na aba Body -> raw -> JSON, envie o objeto abaixo para simular o sensor:

JSON
{ "temperatura": 25, "umidade": 60, "hora": "12:00" }
4.2) Tecnologias Usadas
Node.js — Ambiente de execução que permite rodar o código JavaScript no servidor.

Express — Framework usado para criar as rotas (endpoints) e gerenciar as requisições HTTP da API.

CORS — Pacote de segurança que permite que o servidor aceite requisições vindas do ESP32 ou de outras origens.

JSON — Formato de texto padronizado utilizado para a troca de dados entre o sensor, o servidor e o cliente.

Postman — Ferramenta utilizada para testar o funcionamento das rotas antes de integrar o hardware.

### Reflexão pessoal
o que eu mais gostei foi observar os conceitos do CRUD de POST e GET, pois é muito legal ver as mensagens sendo recebidas, os dados sendo alterados ou apagados. A maior dificuldade foi fazer as próprias modificações no vs code.
