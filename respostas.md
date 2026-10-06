# Respostas · Avaliação Prática de Docker · Cooperativa AgroVale (Turma A)

Nome: SEU NOME COMPLETO AQUI
Matrícula: 26128332
Usuário do GitHub: pagiani
Usuário do Docker Hub: kawan7

Responda com as suas palavras e com o que aconteceu na SUA máquina. Resposta curta e certa vale mais
do que texto longo copiado. Resposta que contradiz o seu próprio Dockerfile ou compose vale zero.

## Parte 1 · Dockerfile do portal

1. Qual imagem base você usou e qual o tamanho final da imagem do portal (saída de `docker images`)?

Usei a imagem oficial `nginx:1.27-alpine`, com tag fixa e sem `latest`. No `docker images` a imagem `kawan7/agrovale-portal:1.0-26128332` ficou com 73.6MB.

2. Em qual pasta do container o Nginx procura os arquivos do site? Mostre o comando que você usou para conferir
   que o `index.html` está lá dentro.

O Nginx serve os arquivos de `/usr/share/nginx/html`. Para conferir, rodei:

```
docker exec teste-portal ls /usr/share/nginx/html
```

e a saída mostrou `50x.html`, `estilo.css` e `index.html`, ou seja, meu site está na pasta certa.

## Parte 2 · Docker Hub

3. Nome completo da imagem publicada e link público do repositório no Docker Hub.

Imagem: `kawan7/agrovale-portal:1.0-26128332`
Link: https://hub.docker.com/r/kawan7/agrovale-portal

4. Por que o `docker login` foi feito com um token de acesso e não com a senha da conta?

Porque o token é uma credencial separada da conta: ele tem permissões limitadas (só o que eu marquei, como ler e escrever) e eu posso revogar ou trocar quando quiser sem mexer na senha da conta. A senha de verdade não fica guardada no computador, que no laboratório é compartilhado. Se o token vazar, só preciso apagar ele.

## Parte 3 · Página de manutenção

5. Preencha uma linha por defeito encontrado. Defeito inexistente listado aqui desconta pontos.

| # | Instrução | O que estava errado | O que você viu acontecer | Como corrigiu |
|---|---|---|---|---|
| 1 | COPY (ausente) | O Dockerfile só tinha `FROM` e `WORKDIR`, não existia nenhum `COPY`, então a página `site/index.html` nunca entrava na imagem | Abri `localhost:7032` e apareceu "Welcome to nginx!". O container estava `Up` no `docker ps -a` | Adicionei `COPY site/ .` no Dockerfile |
| 2 | WORKDIR | Apontava para `/usr/share/nginx`, uma pasta acima de onde o Nginx serve os arquivos (`/usr/share/nginx/html`) | Depois do `COPY`, continuou aparecendo "Welcome to nginx!". O `ls` mostrou meu `index.html` fora da pasta `html` | Troquei o `WORKDIR` para `/usr/share/nginx/html` |

6. Qual a diferença entre `-p 7042:80` e `-p 80:7042` no `docker run`? Qual dos dois números é a porta do container?

No `-p A:B`, o número da esquerda (A) é a porta do meu computador (host) e o da direita (B) é a porta do container. Em `-p 7042:80` eu abro `localhost:7042` e o tráfego vai para a porta 80 do container, que é onde o Nginx escuta. Em `-p 80:7042` seria o contrário, e não funcionaria porque o Nginx não escuta na 7042. A porta do container é a que fica depois dos dois pontos.

## Parte 4 · docker-compose.yml

7. No serviço `blog`, por que `WORDPRESS_DB_HOST` recebe `db` e não `localhost`?

Porque cada container tem o seu próprio `localhost`, que é ele mesmo. O `localhost` dentro do blog aponta para o próprio container do WordPress, onde não tem banco nenhum. Como os serviços estão na mesma rede do Compose, o nome do serviço (`db`) funciona como endereço do container do banco.

8. Por que o serviço `db` não publica a porta 3306? Se precisar consultar o banco, como faz sem publicar
   a porta? Mostre o comando.

Porque só o blog precisa falar com o banco, e isso acontece pela rede interna do Compose. Publicar a 3306 abriria o banco para o computador e para a rede sem necessidade, o que é um risco de segurança. Para consultar sem publicar a porta, entro dentro do container:

```
docker compose exec db mariadb -u root -p
```

## Parte 5 · Persistência

9. Quais comandos você usou para derrubar e subir a stack? Qual comando teria apagado o post que você criou,
   e por quê?

Usei `docker compose down` para derrubar e `docker compose up -d` para subir de novo. O post continuou lá porque os dados do WordPress e do banco ficam em volumes nomeados (`blog_data` e `db_data`), e o `down` só remove os containers e a rede. O comando que teria apagado o post é `docker compose down -v`, porque a opção `-v` remove também os volumes, e junto com eles o banco com o post.

10. Código de conclusão impresso pelo verificador:

```
(cole aqui o código que o verificador imprimir quando passar 16/16)
```
