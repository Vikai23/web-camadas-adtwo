# ADO 2 - Hospedagem da API REST Node.js

**Projeto:** RateYourAlbums

**Aluno:** Vinícius Sousa

**Disciplina:** Aplicação Web em Camadas

---

## 1. Como a API rodava localmente

Antes de hospedar a API, ela rodava no meu próprio computador.

Eu iniciava o servidor com:

```bash
npm run dev
```

Esse comando usava o Nodemon para rodar o arquivo `src/server.js`.

Terminal com a API rodando localmente:

No `.env` local, a API usava essas variáveis:

```env
DATABASE_URL=*****
JWT_SECRET=*****
```

O `.env` real não foi enviado para o GitHub pois tem dados sensíveis, tipo senha do banco e chave JWT. Por isso deixei só o `.env.example`.

Print do `.env.example`:

Também deixei o script `start` no `package.json`, pois ele é usado para iniciar a API fora do ambiente local.

---

## 2. Opções de hospedagem pesquisadas

### Render

O Render permite hospedar API Node.js com Express. Ele tem plano gratuito, mas pode levar alguns segundos para responder à primeira requisição depois de um período sem uso, então às vezes a primeira requisição demora mais.

Escolhi o Render porque eu já tinha usado ele no projeto de PI, então eu já estava mais familiarizado. Como eu já sabia mais ou menos onde mexer, achei melhor usar ele de novo.

### Railway

O Railway também hospeda API Node.js e banco de dados. Eu até pensei em usar, pois vejo bastante gente usando ele, mas acabei descartando porque eu já estava mais acostumado com o Render.

Também vi que ele trabalha mais com créditos e período de teste, então acabei optando pelo Render, que eu já conhecia melhor.

### Koyeb

O Koyeb também suporta aplicação Node.js e Express. Ele tem opção gratuita para projetos pequenos, mas eu não conhecia muito a plataforma.

Como eu queria fazer o deploy sem complicar muito, descartei ele e fiquei com o Render mesmo.

---

## 3. Deploy da API no Render

A hospedagem escolhida foi o Render.

Primeiro entrei no Render, criei um novo Web Service e conectei com o GitHub. Depois selecionei o repositório da minha API, o `RateYourAlbums`.

Render conectado ao repositório:

Na configuração do deploy, usei esses comandos:

```bash
npm install && npx prisma generate
```

Esse foi o comando de build.

E para iniciar a API usei:

```bash
npm start
```

O Render sabe iniciar a API porque no `package.json` tem esse script:

```json
"start": "node src/server.js"
```

Depois configurei as variáveis de ambiente no Render:

```env
DATABASE_URL=*****
JWT_SECRET=*****
```

Variáveis de ambiente no Render, com os valores escondidos:

Depois disso, fiz o deploy.

Deploy concluído no Render:

O Render gerou uma URL pública para acessar minha API.

URL pública funcionando:

Também testei uma rota real da API usando a URL pública:

```txt
https://rateyouralbums-api.onrender.com/albums/1
```

Requisição funcionando em produção:

Essa requisição retornou um álbum com faixas e avaliação, então deu pra ver que a API estava no ar e usando o banco remoto.

---

## 4. Como a API se conecta ao banco hospedado

A API hospedada no Render usa a variável `DATABASE_URL` para conectar no banco.

Essa `DATABASE_URL` é a mesma do banco MySQL da Aiven que eu criei na ADO 1.

Então quando a API está no Render, ela não usa o banco do meu computador. Ela usa o banco remoto da Aiven.

No meu computador essas informações ficam no `.env`. No Render, eu coloquei elas direto no painel de variáveis de ambiente da plataforma.

Isso substitui o `.env` local porque o Render não usa o arquivo `.env` do meu PC. Ele usa as variáveis que eu cadastrei lá no painel.

Se o `DATABASE_URL` estivesse errado, a API até poderia abrir, mas quando tentasse buscar ou salvar dados no banco, daria erro. Por exemplo, uma rota como `/albums` poderia retornar erro interno porque o Prisma não conseguiria conectar no MySQL.

Durante o processo, tive um problema quando abri a URL principal da API e apareceu:

```txt
Cannot GET /
```

Na hora eu achei que tinha quebrado tudo, mas era só porque eu não tinha criado uma rota para `/`.

As rotas `/albums`, `/reviews`, `/tracks` e `/auth` já funcionavam.

Para resolver, coloquei uma rota simples no `server.js`:

```js
app.get('/', (req, res) => {
  return res.status(200).json({
    message: 'RateYourAlbums API funcionando',
    rotas: {
      auth: '/auth',
      albums: '/albums',
      reviews: '/reviews',
      tracks: '/tracks'
    }
  });
});
```

Depois fiz commit, dei push no GitHub e o Render atualizou a API sozinho.
