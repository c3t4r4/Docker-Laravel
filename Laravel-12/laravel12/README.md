# Laravel 12 - Docker Build Multi-Arquitetura

Este README explica como fazer builds Docker para diferentes arquiteturas (AMD64 e ARM64) do Laravel 12.

## Pré-requisitos

- Docker instalado
- Docker Buildx habilitado (disponível por padrão no Docker Desktop)
- Conta no Docker Hub (opcional, para publicar a imagem)

## Verificando Buildx

Primeiro, verifique se o Docker Buildx está disponível:

```bash
docker buildx version
```

Se não estiver disponível, instale:

```bash
docker buildx install
```

## Criando um Builder Multi-Arquitetura

Crie um novo builder que suporte múltiplas arquiteturas:

```bash
docker buildx create --name multiarch-builder --driver docker-container --bootstrap
docker buildx use multiarch-builder
```

Verifique as arquiteturas suportadas:

```bash
docker buildx inspect --bootstrap
```

## Builds para Arquiteturas Específicas

### Build para AMD64 (Intel/AMD)

```bash
# Build simples para AMD64
docker buildx build --platform linux/amd64 -t laravel12:amd64 .

# Build e carregamento local
docker buildx build --platform linux/amd64 -t laravel12:amd64 --load .
```

### Build para ARM64 (Apple Silicon/ARM)

```bash
# Build simples para ARM64
docker buildx build --platform linux/arm64 -t laravel12:arm64 .

# Build e carregamento local
docker buildx build --platform linux/arm64 -t laravel12:arm64 --load .
```

### Build Multi-Arquitetura

```bash
# Build para ambas as arquiteturas
docker buildx build --platform linux/amd64,linux/arm64 -t laravel:laravel-12 .

# Build e push para registry (substitua pelo seu registry)
docker buildx build --platform linux/amd64,linux/arm64 -t c3t4r4/laravel:laravel-12 --push .
```

## Comandos Práticos

### 1. Build Local para Sua Arquitetura

```bash
# Detecta automaticamente sua arquitetura
docker build -t laravel:laravel-12 .
```

### 2. Build e Tag com Versão

```bash
# Build com tag de versão
docker buildx build --platform linux/amd64,linux/arm64 -t laravel:laravel-12 -t laravel:latest .
```

### 3. Build com Cache

```bash
# Build com cache para acelerar builds subsequentes
docker buildx build --platform linux/amd64,linux/arm64 \
  --cache-from type=local,src=/tmp/.buildx-cache \
  --cache-to type=local,dest=/tmp/.buildx-cache \
  -t laravel:cached .
```

### 4. Build apenas para Teste (sem salvar)

```bash
# Build sem criar imagem (apenas para validar)
docker buildx build --platform linux/amd64,linux/arm64 .
```

## Publicando no Docker Hub

### 1. Login no Docker Hub

```bash
docker login
```

### 2. Build e Push Multi-Arquitetura

```bash
# Substitua 'seunome' pelo seu usuário do Docker Hub
docker buildx build --platform linux/amd64,linux/arm64 \
  -t c3t4r4/laravel:latest \
  -t c3t4r4/laravel:laravel-12 \
  --push .
```

## Troubleshooting

### Problema com QEMU

Se encontrar erros relacionados ao QEMU, instale:

```bash
docker run --privileged --rm tonistiigi/binfmt --install all
```

### Problema com Cache

Limpe o cache se houver problemas:

```bash
docker buildx prune
```

### Problema com Registry

Verifique se está logado no registry correto:

```bash
docker buildx build --platform linux/amd64,linux/arm64 \
  -t c3t4r4/laravel:latest \
  --push .
```

## Considerações de Performance

- **AMD64**: Melhor performance em servidores Intel/AMD
- **ARM64**: Melhor performance em Apple Silicon e servidores ARM
- **Multi-arquitetura**: Permite flexibilidade mas aumenta tempo de build

## Automação com GitHub Actions

Para automatizar builds, crie `.github/workflows/docker-build.yml`:

```yaml
name: Docker Build Multi-Arch

on:
  push:
    branches: [ main ]
    tags: [ 'v*' ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v2
    
    - name: Login to Docker Hub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKERHUB_USERNAME }}
        password: ${{ secrets.DOCKERHUB_TOKEN }}
    
    - name: Build and push
      uses: docker/build-push-action@v4
      with:
        context: ./Laravel-12/laravel12
        platforms: linux/amd64,linux/arm64
        push: true
        tags: seunome/laravel12:latest
```

## Links Úteis

- [Docker Buildx Documentation](https://docs.docker.com/buildx/)
- [Multi-platform builds](https://docs.docker.com/build/building/multi-platform/)
- [Docker Hub](https://hub.docker.com/) 