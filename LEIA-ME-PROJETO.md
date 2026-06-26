# Sistema de Processos Juridicos

Este projeto e um site estatico em um unico arquivo principal, usado para organizar processos em Kanban, tarefas, prazos, pagamentos, honorarios e dados de acesso dos clientes.

## Onde fica cada coisa

- `index.html`: arquivo principal do site. Contem HTML, CSS e JavaScript.
- `firebase-database.rules.json`: regras de seguranca do Realtime Database.
- `LEIA-ME-PROJETO.md`: este arquivo, com as instrucoes para abrir, editar, publicar e configurar o projeto.
- `diagnostico.html`: pagina auxiliar para conferir se o arquivo publicado no GitHub Pages esta atualizando.
- `versao.txt`: arquivo simples usado como prova de deploy/cache.
- `antigo/index.html`: copia de uma versao anterior do site.
- `v2/index.html`: versao de referencia usada antes de virar a pagina principal.
- `mockups/`: materiais locais de referencia visual. Esta pasta nao faz parte do site publicado e fica ignorada no Git.

O site publicado fica em:

https://mayslabs.github.io/processos/

O repositorio no GitHub e:

https://github.com/mayslabs/processos

## Como usar em outro computador pelo OneDrive

1. Entre no OneDrive no outro computador com a mesma conta.
2. Aguarde a pasta sincronizar:
   `Documentos > Site dos processos`
3. Abra a pasta no editor de codigo.
4. Edite principalmente o arquivo `index.html`.
5. Para publicar alteracoes no site, e necessario fazer commit e push para o GitHub.

Comandos usados normalmente:

```powershell
git status
git add index.html firebase-database.rules.json LEIA-ME-PROJETO.md .gitignore diagnostico.html versao.txt antigo/index.html v2/index.html
git commit -m "Descricao da alteracao"
git push
```

Depois do `git push`, o GitHub Pages costuma levar alguns segundos ou poucos minutos para atualizar.

Antes de publicar, confira sempre:

```powershell
git status
git diff --stat
```

Se aparecer algum arquivo dentro de `mockups/`, nao publique essa pasta. Ela deve ficar somente local.

## Como testar localmente

Voce pode abrir o `index.html` direto no navegador, mas o ideal e rodar um servidor local dentro da pasta:

```powershell
python -m http.server 4180 --bind 127.0.0.1
```

Depois abra:

```text
http://127.0.0.1:4180/index.html
```

## Login e permissoes

O login usa Firebase Authentication com e-mail e senha.

Importante: a senha nunca aparece no Firebase depois que o usuario e criado. Se esquecer a senha, use redefinicao de senha ou crie um novo usuario.

O site tambem verifica permissoes no Realtime Database, no caminho `users`.

Exemplo de usuario admin:

```json
{
  "users": {
    "UID_DO_USUARIO": {
      "name": "Admin",
      "role": "admin",
      "workspaces": {
        "leticia": true,
        "pessoal": true
      }
    }
  }
}
```

Tipos de usuario:

- `admin`: pode acessar, criar, editar e excluir dados nos ambientes liberados.
- `viewer`: consegue visualizar os ambientes liberados, mas nao deve alterar dados.

## Ambientes do sistema

O site foi dividido em dois ambientes:

- `leticia`: ambiente dos processos com a Leticia, com os dados que ja existiam.
- `pessoal`: ambiente para processos pessoais.

Os dados ficam separados no Firebase em:

```text
workspaces/leticia/kanban
workspaces/pessoal/kanban
```

O seletor de ambiente aparece no topo do site para usuarios que tem acesso a mais de um ambiente.

## Estrutura dos dados no Firebase

A raiz do Realtime Database deve ter, pelo menos:

```json
{
  "users": {},
  "workspaces": {
    "leticia": {
      "kanban": "{\"clients\":[],\"globalTags\":[],\"globalTasks\":[]}"
    },
    "pessoal": {
      "kanban": "{\"clients\":[],\"globalTags\":[],\"globalTasks\":[]}"
    }
  }
}
```

Ainda pode existir um campo antigo chamado `kanban` na raiz. Ele foi mantido como compatibilidade/migracao, mas o sistema novo usa `workspaces`.

## Regras do Firebase

As regras ficam no arquivo:

`firebase-database.rules.json`

Para aplicar:

1. Abra o Firebase Console.
2. Va em `Realtime Database`.
3. Abra a aba `Regras`.
4. Substitua todo o conteudo pelo conteudo de `firebase-database.rules.json`.
5. Clique em `Publicar`.

Nao deixe regras assim em producao:

```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

Isso deixa os dados expostos.

## Funcionalidades principais

- Kanban por colunas.
- Painel lateral de cliente.
- Anotacoes/historico.
- Tarefas do cliente.
- Tarefas internas avulsas, sem cliente.
- Prazos processuais em destaque.
- Pagamentos de cliente em destaque.
- Financeiro com parcelas, pendencias, valores recebidos e alertas laterais.
- Honorarios com campo de valor e calculo percentual.
- Campo de celular com formatacao.
- Separacao entre ambiente `Leticia` e ambiente `Pessoal`.
- Tipos e tribunais especificos para o ambiente `Pessoal`.
- Campo de descricao curta do processo para diferenciar processos da mesma area.
- Campo de parte contraria nos dados do processo.

## Cuidados importantes

- Nao coloque senhas de usuarios neste arquivo.
- Senhas de clientes/GOV.br nao devem ser armazenadas no site.
- Nao publique a pasta `mockups/`.
- Antes de importar JSON no Firebase, exporte um backup.
- Ao mexer em permissoes, confira se o UID do usuario no Authentication e exatamente o mesmo usado em `users`.
- Se trocar/criar usuario no Authentication, o UID muda e precisa ser atualizado no Realtime Database.

## Publicacao

O site e publicado automaticamente pelo GitHub Pages quando ha `git push` para a branch principal.

Depois de publicar, acesse:

https://mayslabs.github.io/processos/

Se a pagina parecer antiga, atualize com `Ctrl + F5` ou espere alguns minutos.
