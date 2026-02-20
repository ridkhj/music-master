# 🎼 Planejamento Técnico: Music Master

Documentação de arquitetura, banco de dados e fluxos do sistema de gestão de repertórios e escalas.

---

## 1. Visão Geral da Arquitetura

A aplicação será dividida em duas partes principais para permitir a evolução futura para aplicativos nativos (Android/iOS):

- **Frontend (Web):** React.js + Tailwind CSS. Interface responsiva focada em usabilidade no altar (tablets/celulares).
- **Backend (API):** Node.js com TypeScript + Express ou NestJS. Responsável pelas regras de negócio e persistência.
- **Banco de Dados:** PostgreSQL (Relacional). Ideal para manter a integridade entre músicas, cantores e tons.
- **Hospedagem Sugerida:** Vercel (Frontend) e Render/Railway (Backend).

---

## 2. Modelagem do Banco de Dados (ER)

### Tabela: `usuarios` (Admin)

| Campo        | Tipo      | Descrição                    |
| :----------- | :-------- | :--------------------------- |
| `id`         | UUID (PK) | Identificador único.         |
| `nome`       | String    | Nome do líder/administrador. |
| `email`      | String    | Email para login.            |
| `senha_hash` | String    | Senha criptografada.         |

### Tabela: `musicas`

| Campo          | Tipo      | Descrição                           |
| :------------- | :-------- | :---------------------------------- |
| `id`           | UUID (PK) | Identificador único.                |
| `titulo`       | String    | Nome da música.                     |
| `bpm`          | Integer   | Batidas por minuto.                 |
| `tom_original` | String    | Tonalidade da versão de referência. |

### Tabela: `versoes` (Relacionada a Musicas)

| Campo        | Tipo      | Descrição                           |
| :----------- | :-------- | :---------------------------------- |
| `id`         | UUID (PK) | Identificador único.                |
| `musica_id`  | UUID (FK) | Relacionamento com a música pai.    |
| `link_url`   | String    | Link do YouTube/Spotify.            |
| `plataforma` | String    | Nome da plataforma (Ex: 'YouTube'). |
| `tom_versao` | String    | Tonalidade específica desta versão. |

### Tabela: `membros` (Equipe)

| Campo    | Tipo      | Descrição                              |
| :------- | :-------- | :------------------------------------- |
| `id`     | UUID (PK) | Identificador único.                   |
| `nome`   | String    | Nome do músico/cantor.                 |
| `tipo`   | Enum      | 'CANTOR' ou 'MUSICO'.                  |
| `funcao` | String    | Ex: 'Tenor', 'Baixista', 'Violonista'. |

### Tabela: `tom_por_cantor` (Regra de Negócio US2)

| Campo       | Tipo      | Descrição                                       |
| :---------- | :-------- | :---------------------------------------------- |
| `membro_id` | UUID (FK) | Referência ao cantor.                           |
| `musica_id` | UUID (FK) | Referência à música.                            |
| `tom_ideal` | String    | A tonalidade que o cantor usa para essa música. |

### Tabela: `escalas` (Setlists)

| Campo         | Tipo      | Descrição                       |
| :------------ | :-------- | :------------------------------ |
| `id`          | UUID (PK) | Identificador único.            |
| `data_evento` | DateTime  | Dia da apresentação.            |
| `descricao`   | String    | Ex: 'Culto de Domingo - Manhã'. |

---

## 3. Rotas do Backend (API REST)

### Músicas

- `GET /songs`: Lista todas as músicas.
- `POST /songs`: Cadastra nova música e suas versões iniciais.
- `GET /songs/:id`: Detalhes, versões e tons salvos por cantor.

### Equipe

- `GET /members`: Lista todos os integrantes.
- `POST /members`: Cadastro de novos músicos/cantores.
- `PUT /members/:id/tones`: Define o tom de uma música específica para aquele cantor.

### Escalas (Setlists)

- `POST /setlists`: Cria uma nova escala para uma data.
- `GET /setlists/:id`: Retorna as músicas, músicos e cantores escalados.
- `GET /setlists/:id/share`: Retorna uma string formatada para envio via WhatsApp.

---

## 4. Definição de Telas (Frontend)

1.  **Dashboard:** Resumo das próximas escalas e estatísticas (total de músicas, total de membros).
2.  **Biblioteca de Músicas:** Listagem com busca, filtro por BPM e visualização de links.
3.  **Gestão de Membros:** Perfil de cada cantor onde é possível ver e editar os tons de cada música.
4.  **Montagem de Escala:** \* Interface de "arrastar e soltar" ou seleção múltipla de músicas.
    - Definição de quem canta o quê (ao selecionar o cantor, o sistema traz automaticamente o tom dele).
    - Botão de compartilhamento rápido.

---

## 5. Próximos Passos

1. Setup do ambiente Node.js com TypeScript.
2. Criação das Migrations no PostgreSQL.
3. Desenvolvimento dos CRUDS básicos.
4. Prototipagem da tela de Escala no React.
