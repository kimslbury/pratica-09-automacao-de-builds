# Automação de Builds e Contêineres na Nuvem

Repositório base para o desenvolvimento de uma automação de build de uma imagem Docker de uma aplicação Python+Flask e implantação em um serviço na nuvem (opcional).

A automação de build consiste no processo de transformar seu código-fonte em artefatos executáveis de forma automática e reproduzível. 
Isto inclui compilar, empacotar e preparar o código para o próximo estágio no pipeline, como seu deploy em um serviço na nuvem.

## Contêineres na Nuvem

**Gitlab Container Registry** é um serviço para armazenamento de imagens Docker, sendo completamente integrado ao GitLab.

**Amazon ECS** é um serviço de orquestração de contêineres, sendo totalmente gerenciado pela AWS.

**Render** é uma plataforma de nuvem que oferece suporte nativo para executar aplicativos e serviços em contêineres.

## Exercícios Propostos

1. **Build da imagem Docker no Gitlab Container Registry**:  
   Possibilitar o build (docker build) e realizar o envio (docker push) da imagem para o Gitlab Container Registry em seu pipeline.

2. **Otimizar o tempo de build**:  
   Possibilitar o uso de cache na construção do contêiner.

3. **(OPCIONAL) Deploy do contêiner em um serviço na nuvem**:  
   Possibilitar o deploy da aplicação em serviços de contêineres como o Amazon ECS² ou Render³.

¹[Trabalhando com Cache no Docker](https://docs.gitlab.com/ee/ci/docker/docker_layer_caching.html)

²[Deploy no Amazon ECS](https://docs.gitlab.com/ee/ci/cloud_deployment/ecs/deploy_to_aws_ecs.html)

³[Deploy no Render](https://render.com/docs/deploy-an-image)

## Executando a Aplicação

Em um ambiente local, executando os comandos:

```
docker build --tag seu-usuário/my-flask-app .
```

```
docker run -d -p 5000:5000 seu-usuário/my-flask-app
```

- A aplicação responderá em duas rotas na web: http://localhost:5000/health-check e http://localhost:5000/hello?name=guijac

## Automação no GitHub Actions

Para manter a estrutura do projeto-base e executar a prática no GitHub, o arquivo
`.github/workflows/build.yml` substitui o pipeline executado originalmente no
GitLab CI. O arquivo `.gitlab-ci.yml` original foi mantido como referência do
material-base e não é executado pelo GitHub.

Em cada push para `main`, o workflow:

1. instala as dependências e executa os testes Python;
2. configura o Docker Buildx;
3. autentica no GitHub Container Registry usando `GITHUB_TOKEN`;
4. constrói a imagem com as tags `latest` e `sha-<commit>`;
5. publica a imagem em `ghcr.io/<proprietario>/<repositorio>`;
6. reutiliza o cache de camadas por meio do cache do GitHub Actions.

O build remoto usa o runner Ubuntu do GitHub Actions. A execução local requer
Docker Desktop com a virtualização habilitada no Windows.

Para validar o cache, uma nova execução do workflow deve ser disparada após
uma alteração no código. O resumo do Docker Build registra as camadas
reaproveitadas e permite comparar a duração com a primeira execução.
