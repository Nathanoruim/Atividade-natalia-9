# Relatório — Projeto DevOps - Versão 2.0

## Objetivo

Atualizar o site executado no Podman para a versão 2.0, substituindo o contêiner antigo que servia a página padrão do nginx na porta 8888 e confirmando o novo conteúdo e o estilo.

## Passos e resultados observados

1. O `Containerfile` foi preparado para copiar `index.html` e `style.css` em uma imagem nginx:alpine.
2. A página v2.0 contém o título e o H1 `Projeto DevOps - Versão 2.0 (Container)`.
3. O CSS define o fundo como `#e8f5e9`.

### Comandos Podman efetivamente executados

```powershell
podman --version
podman images
podman ps -a
podman inspect site-aluno
podman inspect site-nathan-novo
podman build -t meu-site-v2 -f Containerfile .
```

O build foi concluído com sucesso. A imagem criada é `localhost/meu-site-v2:latest`, com ID `a0aabd188c92`.

Antes da remoção, a tentativa abaixo foi feita com o nome antigo ainda ocupado:

```powershell
podman run -d --name site-aluno -p 8888:80 meu-site-v2
```

Resultado: falhou com erro de nome já em uso, porque o contêiner antigo `site-aluno` já existia. A tentativa não removeu nem alterou o contêiner existente.

O contêiner antigo tinha ID `9d264d9ad170`, usava uma imagem antiga com ID iniciado em `dea3689e...`, estava em execução e ocupava a porta 8888. Ele servia a página padrão de boas-vindas do nginx.

Outro contêiner, `romantic_lewin`, estava separado na porta 9000 e não foi removido. Também existia `site-nathan-novo`, em estado `Created`, com a imagem `meu-sitev3`; a tentativa desse contêiner foi registrada como falha por endereço da porta 8888 já ocupado e ele permaneceu intacto.

O contêiner antigo foi removido explicitamente:

```powershell
podman stop site-aluno
podman rm site-aluno
podman ps -a --filter name=site-nathan-novo
```

Após confirmar que a porta 8888 estava livre, o novo contêiner foi iniciado:

```powershell
podman run -d --name site-aluno -p 8888:80 meu-site-v2
podman ps --filter name=site-aluno
podman inspect site-aluno
```

Resultado: contêiner novo em execução, com ID iniciado em `f43d6e516c0a...`.

## Verificação

- A porta 8888 ficou disponível antes da nova inicialização.
- A página HTTP na porta 8888 respondeu com status 200.
- O endpoint CSS respondeu com status 200.
- O título e o H1 `Projeto DevOps - Versão 2.0 (Container)` foram confirmados no novo site.
- A nova cor de fundo `#e8f5e9` foi confirmada.

## Reflexões

1. **Se eu deletar o arquivo `index.html` da minha pasta, o site que está rodando vai cair? Por quê?**  
   Não. O `Containerfile` copia o HTML para dentro da imagem durante o build, e o contêiner serve essa cópia. Como não há montagem da pasta do projeto no contêiner, apagar o arquivo local não remove a cópia que já está na imagem. O arquivo será necessário para construir uma nova imagem.

2. **Qual a diferença entre uma imagem e um container?**  
   A imagem é o modelo imutável que contém os arquivos e as instruções para executar a aplicação. O contêiner é uma instância criada a partir dessa imagem, em execução ou parada, com estado e configuração próprios, como nome e portas publicadas.

## Referências de prints

As capturas solicitadas não puderam ser salvas como arquivos dentro de `evidencias/`. Nenhum print foi inventado ou substituído por imagem ilustrativa; portanto, os seis arquivos abaixo continuam pendentes:

- [01-index-html.png](./evidencias/01-index-html.png) — **AUSENTE / evidência pendente**.
- [02-criacao-imagem.png](./evidencias/02-criacao-imagem.png) — **AUSENTE / evidência pendente**.
- [03-erro-conflito-nome.png](./evidencias/03-erro-conflito-nome.png) — **AUSENTE / evidência pendente**.
- [04-remocao-container-antigo.png](./evidencias/04-remocao-container-antigo.png) — **AUSENTE / evidência pendente**.
- [05-container-em-execucao.png](./evidencias/05-container-em-execucao.png) — **AUSENTE / evidência pendente**.
- [06-site-versao-2.png](./evidencias/06-site-versao-2.png) — **AUSENTE / evidência pendente**.
