# Inventário — Projeto em Haskell

## Instituição

**Pontifiicia Universidade Catolica do Parana**

## Disciplina

**Ciencia Da Computacao**

## Professor

**Frank Coelho De Alcantara**

## Alunos (em ordem alfabética)

* **Adryan Costa** — GitHub: [@AdryanCostaSilva](https://github.com/AdryanCostaSilva)
* **Hassan Ali** — GitHub: [@haskaz313](https://github.com/haskaz313)
* **Hussein Ali** — GitHub: [@ItsPoyoyo](https://github.com/ItsPoyoyo)
* **Murilo Zimerman** — GitHub: [@MuriloZF](https://github.com/MuriloZF)

---

## Visão geral do projeto

Este repositório contém um sistema de inventário implementado em **Haskell**. O programa permite adicionar, remover, listar itens e gerar relatórios sobre o estado do inventário e operações realizadas.

Ele foi pensado para ser simples, modular e didático — ideal para aprendizado de manipulação de estruturas, IO e persistência básica em Haskell.

---

## Principais funcionalidades

* Adicionar novo item ao inventário
* Remover quantidade de um item existente
* Listar todos os itens atualmente cadastrados
* Gerar relatório completo com estatísticas (total de itens, operações, item mais movimentado)
* Log das operações (sucesso/erro)

---

## Arquitetura do código (explicação)

O projeto segue uma arquitetura modular simples, dividindo responsabilidades por módulos:

### Módulos e responsabilidades (exemplo)

* `Main.hs` — ponto de entrada da aplicação. Inicializa o programa, carrega estado/persistência, entra no loop de leitura de comandos e faz o dispatch para os handlers.

* `Types.hs` — define os tipos principais do sistema (por exemplo `Item`, `Inventario`, `Operacao`, `Categoria`). Ter tipos bem definidos torna o restante do código mais claro e seguro.

* `Inventory.hs` (ou `Operacoes.hs`) — contém as funções puras para manipulação do inventário:

  * `addItem :: Inventario -> Item -> Inventario`
  * `removeItem :: Inventario -> ItemId -> Quantidade -> Either Erro Inventario`
  * `listItems :: Inventario -> [Item]`
  * `moveCountLog :: Inventario -> ItemId -> Inventario` (ex.: incrementa contador de movimentações)

* `Persistence.hs` — funções de leitura e escrita do estado em arquivo (por exemplo, serialização usando `show`/`read` ou JSON via Aeson):

  * `saveInventory :: FilePath -> Inventario -> IO ()`
  * `loadInventory :: FilePath -> IO Inventario`

* `Parser.hs` (ou `IOHandler.hs`) — lida com a leitura das entradas do usuário (linha por linha), parse dos comandos (add, remove, listar, report) e validação dos dados.

* `Logger.hs` — registra logs de operações bem-sucedidas e falhas em arquivos separados (ex.: `logs_success.txt`, `logs_error.txt`).

### Fluxo de dados

1. `Main` carrega o inventário salvo (se existir) via `Persistence.loadInventory`.
2. O programa exibe um prompt e aguarda comandos do usuário.
3. A entrada é passada para `Parser`, que retorna uma estrutura de comando (`Command`) ou um erro de parse.
4. `Main` chama a função apropriada em `Inventory` para executar a operação (puras quando possível).
5. Alterações no inventário são persistidas com `Persistence.saveInventory` e o resultado é registrado via `Logger`.
6. O loop continua até o comando de saída.

Essa separação facilita testes unitários nas funções puras (`Inventory`) e mantém o código de IO isolado.

---

## Estrutura de arquivos (sugestão)

```
inventario-haskell/
├── src/
│   ├── Main.hs
│   ├── Types.hs
│   ├── Inventory.hs
│   ├── Persistence.hs
│   ├── Parser.hs
│   └── Logger.hs
├── data/
│   ├── inventory.db      # arquivo de persistência (padrão do projeto)
│   └── logs/
│       ├── success.log
│       └── error.log
├── test/                 # testes unitários (se houver)
├── README.md
└── stack.yaml / package.yaml
```

> Observação: Se o projeto atual não tiver todos esses módulos implementados, esta estrutura serve como orientação para modularizar e melhorar o código.

---

## Como compilar e executar

### Executar no OnlineGDB (já configurado)

1. Abra: 👉 [**OnlineGDB**](https://onlinegdb.com/IujVRtNv0K)
2. O código já estará carregado. Clique em **Run**.

### Compilar localmente com GHC (modo simples)

1. Tenha GHC instalado (GHC >= 8.x).
2. No terminal, posicione-se na pasta do projeto e rode:

```bash
ghc -o inventario Main.hs
./inventario
```

### Usando Stack (recomendado para projetos Haskell)

1. Instale o [Stack](https://docs.haskellstack.org).
2. Crie/edite `stack.yaml` e `package.yaml` conforme necessário.
3. Rode:

```bash
stack build
stack exec inventario
```

---

## Dependências

* GHC (Glasgow Haskell Compiler)
* (Opcional) Stack
* (Opcional) Biblioteca `aeson` se for usada serialização JSON

---

## Exemplo de uso (mantido do README original)

> Aqui mantivemos os exemplos de entrada e saída já presentes no README. Eles demonstram as operações básicas: `add`, `remove`, `listar`, `report`.

### **Adicionar um item**

**Entrada:**

```
add
ID: A1
Nome: Teclado Mecânico
Quantidade: 10
Categoria: Periféricos
```

**Saída:**

```
Operacao realizada com sucesso!
```

### **Remover quantidade de um item**

**Entrada:**

```
remove
ID do item: A1
Quantidade a remover: 3
```

**Saída:**

```
Operacao realizada com sucesso!
```

### **Listar itens**

**Entrada:**

```
listar
```

**Saída:**

```
=== Itens no Inventario ===
ID: A1 | Nome: Teclado Mecânico | Qtd: 7 | Categoria: Periféricos
```

### **Gerar relatório**

**Entrada:**

```
report
```

**Saída:**

```
=== Relatorio Completo ===
Total de itens no inventario: 1
Total de operacoes registradas: 3
--- Itens no Inventario ---
ID: A1 | Nome: Teclado Mecânico | Qtd: 7 | Categoria: Periféricos
--- Logs de Sucesso ---
Total de operacoes bem-sucedidas: 3
...
--- Item Mais Movimentado ---
Item: ID=A1 | Nome=Teclado Mecânico | Operacoes=3
```

---

## Passo a passo visual de uso (rápido)

1. Inicie o programa (`Run` no OnlineGDB ou execute o binário local).
2. No prompt, digite um comando (`add`, `remove`, `listar`, `report`, `exit`).
3. Siga as instruções que o programa pede (IDs, quantidades, nomes etc.).
4. Após cada operação, verifique a saída e/ou os arquivos de log em `data/logs/`.
5. Para persistir alterações (se o programa não salvar automaticamente), use o comando `save` (se implementado) ou saia e confirme a gravação.

---

## Testes

* Recomenda-se escrever testes unitários para as funções em `Inventory.hs` (funções puras) usando `hspec` ou `tasty`.
* Testes IO podem ser feitos com `tasty` + `tasty-hunit` ou simulando arquivos temporários.

---

## Como contribuir

1. Fork este repositório.
2. Crie uma branch com a sua feature ou correção: `git checkout -b feature/nova-funcionalidade`.
3. Abra um Pull Request descrevendo a mudança.

---

## Contato

Dúvidas, sugestões ou problemas: abra uma issue no GitHub do projeto ou fale com os autores listados acima.

---

Se quiser, eu posso:

* Gerar um diagrama simples (ASCII ou imagem) mostrando o fluxo entre módulos.
* Adicionar instruções específicas para o `onlinegdb` (por exemplo: argumentos de execução, entradas de exemplo pré-carregadas).
* Converter exemplos para um script de teste automatizado.

Diga qual dessas opções prefere que eu adicione a este README e eu já atualizo.

## Arquitetura do Código

O sistema foi desenvolvido em Haskell seguindo uma organização clara entre lógica pura, entrada e saída, persistência e auditoria. A seguir, é detalhada a arquitetura implementada.

### 1. Estrutura Geral

O código é dividido em quatro componentes principais:

* **Modelos de dados (Tipos):** estruturas fundamentais como `Item`, `Inventario`, `LogEntry`, `AcaoLog` e `StatusLog`.
* **Lógica de negócio (funções puras):** funções que realizam validações e operações sem efeitos colaterais.
* **Persistência (I/O):** leitura e gravação de arquivos com inventário e logs.
* **Interação com o usuário:** loop principal que interpreta comandos e aciona as funções puras.

### 2. Modelos de Dados

As estruturas de dados representam o estado do sistema:

* `Item` contém ID, nome, quantidade e categoria.
* `Inventario` é um `Map String Item` para acesso eficiente.
* `LogEntry` registra cada operação com horário, ação, status e detalhes.

### 3. Lógica Pura

Inclui funções como:

* `addItem`
* `removeItem`
* `deleteItem`
* `updateQty`

Essas funções processam dados, validam regras e retornam resultados no formato `Either String (Inventario, LogEntry)`, sem realizar leitura ou escrita de arquivos. Isso garante previsibilidade e facilidade de teste.

### 4. Persistência

Responsável por salvar e carregar dados dos arquivos:

* `Inventario.dat` guarda todo o inventário serializado.
* `Auditoria.log` armazena cada operação realizada.

As funções de persistência utilizam:

* `readMaybe` para evitar erros ao ler arquivos.
* `catch` para tratar ausência de arquivos.
* `seq` para evitar bloqueios de acesso.

### 5. Auditoria e Relatórios

O sistema inclui um módulo de análise de logs capaz de:

* Identificar operações com erro.
* Identificar operações com sucesso.
* Filtrar histórico de um item específico.
* Descobrir o item mais movimentado.

Funções importantes:

* `logsDeErro`
* `logsDeSucesso`
* `historicoPorItem`
* `itemMaisMovimentado`

### 6. Loop Principal

O programa funciona em um ciclo contínuo que:

1. Aguarda um comando do usuário.
2. Coleta dados necessários.
3. Chama a função pura correspondente.
4. Atualiza inventário e logs se a operação for bem-sucedida.

Comandos implementados:

* `add`
* `remove`
* `delete`
* `update`
* `listar`
* `report`
* `historico`
* `sair`

### 7. Fluxo de Operação (Exemplo: add)

1. Usuário digita `add`.
2. O sistema solicita ID, nome, quantidade e categoria.
3. A função pura `addItem` é executada.
4. Se for válida, o inventário é salvo e o log registrado.
5. Se houver erro, somente o log de falha é registrado.

### 8. Estrutura dos Arquivos

O sistema utiliza dois arquivos:

* `Inventario.dat`: armazena o inventário completo.
* `Auditoria.log`: armazena os registros de ação linha a linha.

Ambos utilizam serialização com `Show` e `Read`, permitindo leitura direta pelo Haskell.

### 9. Características Técnicas

* Uso de `Map` garantindo eficiência nas operações.
* Controle completo de erros via `Either`.
* Separação entre lógica pura e I/O.
* Uso de `UTCTime` para auditoria confiável.
* Análise estatística para determinar o item mais movimentado.
