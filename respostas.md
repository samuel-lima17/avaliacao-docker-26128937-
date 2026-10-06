# Respostas · Avaliação Prática de Docker · Cooperativa AgroVale (Turma A)

Nome: Aluno
Matrícula: 26128937
Usuário do GitHub: samuel-lima17
Usuário do Docker Hub: osiqueira

Responda com as suas palavras e com o que aconteceu na SUA máquina. Resposta curta e certa vale mais
do que texto longo copiado. Resposta que contradiz o seu próprio Dockerfile ou compose vale zero.

## Parte 1 · Dockerfile do portal

1. Qual imagem base você usou e qual o tamanho final da imagem do portal (saída de `docker images`)?

Utilizei a imagem base `nginx:1.27-alpine`. O tamanho final da imagem gerada é de aproximadamente 23.5 MB.

2. Em qual pasta do container o Nginx procura os arquivos do site? Mostre o comando que você usou para
   conferir que o `index.html` está lá dentro.

O Nginx procura os arquivos em `/usr/share/nginx/html/`. Para conferir o arquivo dentro do container em execução, utilizei o comando:
`docker exec avaliacao-docker-agrovale-portal-1 ls -la /usr/share/nginx/html/`

## Parte 2 · Docker Hub

3. Nome completo da imagem publicada e link público do repositório no Docker Hub.

Nome da imagem: `osiqueira/agrovale-portal:1.0-26128937`
Link do repositório: `https://hub.docker.com/r/osiqueira/agrovale-portal`

4. Por que o `docker login` foi feito com um token de acesso e não com a senha da conta?

Porque o Personal Access Token (PAT) é mais seguro que a senha principal, permite limitar permissões específicas (escopo) e pode ser revogado a qualquer momento sem a necessidade de alterar a senha da conta do Docker Hub.

## Parte 3 · Página de manutenção

5. Preencha uma linha por defeito encontrado. Defeito inexistente listado aqui desconta pontos.

| # | Instrução | O que estava errado | O que você viu acontecer | Como corrigiu |
|---|---|---|---|---|
| 1 | `WORKDIR` | Caminho definido como `/usr/share/nginx` (faltava a pasta `html`) | Os arquivos eram copiados fora do diretório padrão servido pelo Nginx | Corrigido para `COPY site/ /usr/share/nginx/html/` |
| 2 | `COPY` | Ausência da instrução de cópia dos arquivos do site no Dockerfile | O container subia sem os arquivos estáticos da página de manutenção | Adicionada a instrução `COPY site/ /usr/share/nginx/html/` |
| 3 | `CMD` | Comando/Script incorreto no final | O container encerrava imediatamente após iniciar (status `Exited`) | Removida a instrução incorreta e mantido o comando padrão da imagem base |

6. Qual a diferença entre `-p 7042:80` e `-p 80:7042` no `docker run`? Qual dos dois números é a porta do container?

A sintaxe é `-p <porta_hospedeiro>:<porta_container>`.
Em `-p 7042:80`, a porta 7042 do computador é redirecionada para a porta 80 do container.
Em `-p 80:7042`, a porta 80 do computador é redirecionada para a porta 7042 do container.
O número após os dois pontos é a porta interna do container (no caso de `-p 7042:80`, a porta do container é a 80).

## Parte 4 · docker-compose.yml

7. No serviço `blog`, por que `WORDPRESS_DB_HOST` recebe `db` e não `localhost`?

Porque na rede interna criada pelo Docker Compose (`agrovale-net`), os containers se comunicam através do serviço de resolução de nomes interno (DNS) usando o nome do serviço. Se usasse `localhost`, o WordPress procuraria o banco dentro do próprio container do WordPress.

8. Por que o serviço `db` não publica a porta 3306? Se precisar consultar o banco, como faz sem publicar
   a porta? Mostre o comando.

Por questão de segurança, para evitar expor o banco de dados diretamente para fora da rede do Docker.
Para consultar o banco sem publicar a porta, executa-se o cliente do MariaDB direto dentro do container:
`docker compose exec db mariadb -u agrovale -p agrovale_blog`

## Parte 5 · Persistência

9. Quais comandos você usou para derrubar e subir a stack? Qual comando teria apagado o post que você criou,
   e por quê?

Para derrubar e subir mantendo os dados:
`docker compose down`
`docker compose up -d`

O comando que teria apagado o post criado seria `docker compose down -v`.
Isso acontece porque a opção `-v` (ou `--volumes`) remove os volumes nomeados associados (`db_data` e `blog_data`), apagando os dados persistidos do MariaDB e do WordPress.

10. Código de conclusão impresso pelo verificador:

================================================================
 Verificador · Avaliação Prática de Docker · Turma A
================================================================
 Matrícula 26128937 · portal 8037 · blog 9037 · manutenção 7037

A. Arquivos, imagens e Git
[ OK ] A1 portal/Dockerfile segue os requisitos
[ OK ] A2 imagem manutencao:26128937 corrigida e servindo o aviso
[ OK ] A3 .env fora do Git e .env.example versionado
[ OK ] A4 5+ commits e remoto no GitHub (encontrados: 5)
[ OK ] A5 imagem osiqueira/agrovale-portal:1.0-26128937 pública no Docker Hub

B. Stack em execução
[ OK ] B1 serviços portal, blog e db em execução
[ OK ] B2 portal roda a imagem publicada
[ OK ] B3 portas: portal em 8037 e blog em 9037
[ OK ] B4 db sem porta publicada e com volume nomeado
[ OK ] B5 blog com volume nomeado em /var/www/html
[ OK ] B6 rede própria compartilhada pelos três serviços
[ OK ] B7 política de restart nos três serviços
[ OK ] B8 nenhuma senha escrita direto no docker-compose.yml

C. Conteúdo e persistência
[ OK ] C1 portal mostra seu nome e sua matrícula
[ OK ] C2 WordPress instalado com a matrícula no título do site
[ OK ] C3 post sobreviveu à recriação do blog (post 2026-10-06T00:22:00 · container 2026-10-06T00:25:29)

================================================================
 Resultado: 16/16 verificações
 Código de conclusão: AGROVALE-26128937-1DE0D404
 Copie o código para o respostas.md, faça o commit final e crie a tag v1.0.
================================================================
PS C:\Users\Aluno\Downloads\01.A - Projeto_Avaliação\01.A - Projeto_Avaliação\01.1 - Projeto_Avaliação\avaliacao-docker-agrovale>