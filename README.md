# ADO 2 - Hospedagem da API REST Node.js

**Projeto:** RateYourAlbums

**Aluno:** Vinícius Sousa

**Disciplina:** Aplicação Web em Camadas

---

## 1. Como a API rodava localmente

Antes de hospedar a API, ela rodava no meu próprio computador mesmo.

Eu iniciava o servidor com:

```bash
npm run dev
```

Esse comando usa o Nodemon, tipo ele fica rodando o `src/server.js` e quando eu mexo em alguma coisa ele reinicia o servidor sozinho.

Terminal com a API rodando localmente:

![API local terminal](./imgs/api-local-terminal.png)

No `.env` local, a API usava essas variáveis:

```env
DATABASE_URL=*****
JWT_SECRET=*****
```

O `.env` real não foi enviado para o GitHub pois tem dados sensíveis, tipo senha do banco e chave JWT. Por isso deixei só o `.env.example`, que mostra quais variáveis precisa ter mas sem mostrar os valores de verdade.

Print do `.env.example`:

![Env Example](./imgs/env-example.png)

Também deixei o script `start` no `package.json`, pois ele é o comando que a plataforma usa para ligar a API fora do meu computador.

![Package JSON Start](./imgs/package-json-start.png)

---

## 2. Opções de hospedagem pesquisadas

### Render

O Render permite hospedar API Node.js com Express. Ele tem plano gratuito, mas pelo que vi ele pode ficar meio parado quando ninguém usa por um tempo, daí a primeira requisição pode demorar um pouco mais.

Escolhi o Render porque eu já tinha usado ele no projeto de PI, então eu já estava mais familiarizado. Como eu já sabia mais ou menos onde mexer, achei melhor usar ele de novo ao invés de ir para uma plataforma que eu nunca tinha mexido.

### Railway

O Railway também hospeda API Node.js e banco de dados. Eu até pensei em usar, pois vejo bastante gente usando ele, mas acabei descartando porque eu estava mais acostumado com o Render.

Também vi que ele trabalha mais com créditos e período de teste, então fiquei meio assim de usar nessa entrega. Preferi o Render pois eu já conhecia melhor.

### Koyeb

O Koyeb também suporta aplicação Node.js e Express. Ele tem opção gratuita para projetos pequenos, então em teoria também dava para usar.

Mas eu não conhecia muito a plataforma, então achei que eu ia perder mais tempo tentando entender onde mexer. Como a ideia era subir a API sem complicar demais, fiquei com o Render mesmo.

---

## 3. Deploy da API no Render

A hospedagem escolhida foi o Render.

Primeiro entrei no Render, criei um novo Web Service e conectei com o GitHub. Depois selecionei o repositório da minha API, o `RateYourAlbums`.

Render conectado ao repositório:

![Render Repo](./imgs/render-repo.png)

Na configuração do deploy, usei esses comandos:

```bash
npm install && npx prisma generate
```

Esse foi o comando de build, tipo a parte que instala as dependências e prepara o Prisma antes da API ligar.

E para iniciar a API usei:

```bash
npm start
```

O Render sabe iniciar a API porque no `package.json` tem esse script:

```json
"start": "node src/server.js"
```

Ou seja, quando o Render roda `npm start`, ele acaba rodando o `server.js`, que é onde minha API começa.

Depois configurei as variáveis de ambiente no Render:

```env
DATABASE_URL=*****
JWT_SECRET=*****
```

Variáveis de ambiente no Render, com os valores escondidos:

![Render Env Vars](./imgs/render-env-vars.png)

Depois disso, fiz o deploy.

Deploy concluído no Render:

![Render Deploy Success](./imgs/render-deploy-success.png)

O Render gerou uma URL pública para acessar minha API, tipo um link que qualquer navegador consegue abrir.

URL pública funcionando:

![Render Public URL](./imgs/render-public-url.png)

Também testei uma rota real da API usando a URL pública:

```txt
https://rateyouralbums-api.onrender.com/albums/1
```

Requisição funcionando em produção:

![Requisição API Produção](./imgs/requisicao-api-producao.png)

Essa requisição retornou um álbum com faixas e avaliação, então deu pra ver que a API estava no ar e usando o banco remoto.

---

## 4. Como a API se conecta ao banco hospedado

A API hospedada no Render usa a variável `DATABASE_URL` para conectar no banco.

Essa `DATABASE_URL` é tipo o caminho do banco. No meu caso, ela é a URL do banco MySQL da Aiven que eu criei na ADO 1.

Então quando a API está no Render, ela não usa o banco do meu computador. Ela usa o banco remoto da Aiven.

No meu computador essas informações ficam no `.env`. No Render, eu coloquei elas direto no painel de variáveis de ambiente da plataforma.

Isso substitui o `.env` local porque o Render não usa o arquivo `.env` do meu PC. Ele usa as variáveis que eu cadastrei lá no painel.

Se o `DATABASE_URL` estivesse errado, a API até poderia abrir, mas quando tentasse buscar ou salvar dados no banco, daria erro. Tipo, uma rota como `/albums` poderia retornar erro interno porque o Prisma não conseguiria achar o MySQL da Aiven.

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
