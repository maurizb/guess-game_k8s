
## Jogo Multiplayer de senha

Este projeto é um jogo multiplayer simples rodando em containers Docker. O jogador deve adivinhar uma senha criada aleatoriamente, e o sistema fornecerá feedback sobre o número de letras corretas e suas respectivas posições.

## Requisitos

Antes de começar, certifique-se de que você tem as seguintes ferramentas instaladas:

- [Docker Desktop](https://docs.docker.com/get-docker/)

## Funcionalidades

Utilizamos **Kubernetes** para criar múltiplos containers de backend para balanceamento. O objetivo é distribuir as requisições entre diferentes instâncias do backend.

## Instalação

Siga os passos abaixo para instalar e rodar o projeto em sua máquina:

### 1. Clone o repositório

```bash
git clone https://github.com/maurizb/guess-game_k8s.git
cd guess-game_k8s
```

### 2. Rodar o projeto

Para rodar o projeto, tenha o Docker Desktop aberto e certifique-se que a opção de "enable Kubernetes" está ativa. Utilize então os seguintes comandos:

```bash
kubectl apply -f frontend-deploy.yaml
kubectl apply -f backend-deploy.yaml
kubectl apply -f db-deploy.yaml
kubectl apply -f hpa.yaml
```

Isso irá subir os containers do jogo. A aplicação estará disponível em [http://localhost](http://localhost).


## Como Jogar

1. Abra o navegador e acesse [http://localhost](http://localhost). Digite uma frase secreta, envie, e salve o game-id.

2. Para adivinhar a senha, entre com o game_id que foi gerado no passo acima e tente adivinhar.
3. Toda vez que você interagir com o jogo, o Nginx irá redirecionar sua requisição para um dos containers de backend rodando no ambiente.
   
## Encerrando o Jogo

Para parar todos os containers e excluir os volumes, executar:

```bash
kubectl delete deployment front-deployment
kubectl delete deployment backend
kubectl delete deployment db-deployment
kubectl delete hpa hpa-backend
```

Isso irá desligar e remover todos os containers criados.


# Decisões de Design da Aplicação

O foco foi em criar uma aplicação escalável, eficiente e fácil de manter, utilizando Kubernetes para balanceamento de carga.

- **Isolamento**: Cada serviço (backend e proxy) é isolado em seu próprio container, evitando conflitos de dependências e facilitando o gerenciamento. 
- **Portabilidade**: Os containers garantem que a aplicação rodará da mesma maneira em qualquer ambiente, seja de desenvolvimento, testes ou produção, sem dependências externas além do Docker.
- **Escalabilidade**: Com o Docker, é fácil aumentar o número de instâncias de qualquer serviço, permitindo escalar a aplicação horizontalmente conforme a demanda. Foram usados 2 backends por padrão no docker-compose.yml (backend1 e backend2).

Utilizei o Kubernetes para **orquestrar** os containers. Ele facilita a definição, deploy e gerenciamento dos serviços da aplicação.

- **Multicontainers**: A aplicação utiliza múltiplos containers de backend, e o Docker Compose coordena a criação e conexão desses containers com outros serviços, como o Nginx.
- **Redundância**: O uso de múltiplos containers backend permite redundância, melhorando a resiliência e disponibilidade.