# Aula Prática – Do DER ao Banco de Dados no SQLite

## 🎯 Objetivo da aula

Cronograma da aula:

* Compreender a transformação de um **DER em tabelas relacionais**
* Criar tabelas com **chave primária**
* Criar **chaves estrangeiras**
* Inserir dados em tabelas
* Consultar dados com **JOIN**
* Entender como a **tabela lógica conecta tabelas físicas**


# 1️⃣ Analisando o DER

O diagrama possui:

### Entidade: Empregado

Atributos:

* ID (chave primária)
* RG
* Nome
* CPF
* Depto
* Cargo
* Salário

### Entidade: Projeto

Atributos:

* Código (chave primária)
* Data_Inicio
* Data_Termino
* Orçamento_Total

### Relacionamento: Participar

Cardinalidade:

Empregado **(0,n)** ← participar → **(1,n)** Projeto

Isso significa:

* Um empregado pode participar de vários projetos
* Um projeto pode ter vários empregados

Logo temos um relacionamento **N:N (muitos para muitos)**.

Para resolver isso no modelo relacional precisamos criar uma **tabela intermediária**.

---

# 2️⃣ Acessar o ambiente SQL

Abrir:

**SQLite Online**

Criar um novo banco de dados.

---

# 3️⃣ Ativar as chaves estrangeiras

No SQLite elas não funcionam automaticamente.

Executar:

```sql
PRAGMA foreign_keys = ON;
```

---

# 4️⃣ Criar as tabelas físicas

## Tabela Empregado

```sql
CREATE TABLE Empregado (
    id INTEGER PRIMARY KEY,
    rg TEXT,
    nome TEXT,
    cpf TEXT,
    depto TEXT,
    cargo TEXT,
    salario REAL
);
```

---

## Tabela Projeto

```sql
CREATE TABLE Projeto (
    codigo INTEGER PRIMARY KEY,
    data_inicio DATE,
    data_termino DATE,
    orcamento_total REAL
);
```

---

# 5️⃣ Criar a tabela lógica (relacionamento)

Tabela **Participar**

```sql
CREATE TABLE Participar (
    id_empregado INTEGER,
    codigo_projeto INTEGER,

    PRIMARY KEY (id_empregado, codigo_projeto),

    FOREIGN KEY (id_empregado)
        REFERENCES Empregado(id),

    FOREIGN KEY (codigo_projeto)
        REFERENCES Projeto(codigo)
);
```

Explicação:

* **id_empregado → referencia Empregado**
* **codigo_projeto → referencia Projeto**

Essa tabela **conecta as duas entidades**.

---

# 6️⃣ Inserindo dados nas tabelas

## Inserindo empregados

```sql
INSERT INTO Empregado VALUES
(1,'123','João','11111111111','TI','Programador',5000),
(2,'456','Maria','22222222222','TI','Analista',6000),
(3,'789','Carlos','33333333333','Financeiro','Contador',4500);
```

---

## Inserindo projetos

```sql
INSERT INTO Projeto VALUES
(10,'2026-01-01','2026-06-01',100000),
(20,'2026-02-01','2026-08-01',150000);
```

---

## Inserindo participações

```sql
INSERT INTO Participar VALUES
(1,10),
(2,10),
(2,20),
(3,20);
```

Significado:

* João trabalha no projeto 10
* Maria trabalha no projeto 10 e 20
* Carlos trabalha no projeto 20

---

# 7️⃣ Consultando dados das tabelas

## Ver empregados

```sql
SELECT * FROM Empregado;
```

---

## Ver projetos

```sql
SELECT * FROM Projeto;
```

---

## Ver relacionamento

```sql
SELECT * FROM Participar;
```

Aqui vemos apenas os **IDs conectados**.

---

# 8️⃣ Consultando dados combinados (JOIN)

Agora vamos mostrar como a tabela lógica **acessa as tabelas físicas**.

Consulta:

```sql
SELECT 
Empregado.nome,
Projeto.codigo
FROM Participar
JOIN Empregado
ON Participar.id_empregado = Empregado.id
JOIN Projeto
ON Participar.codigo_projeto = Projeto.codigo;
```

Resultado esperado:

| nome   | codigo |
| ------ | ------ |
| João   | 10     |
| Maria  | 10     |
| Maria  | 20     |
| Carlos | 20     |

---

# 9️⃣ Consulta mais completa

Mostrar:

* Nome do empregado
* Departamento
* Código do projeto
* Orçamento

```sql
SELECT 
Empregado.nome,
Empregado.depto,
Projeto.codigo,
Projeto.orcamento_total
FROM Participar
JOIN Empregado
ON Participar.id_empregado = Empregado.id
JOIN Projeto
ON Participar.codigo_projeto = Projeto.codigo;
```

Agora conseguimos ver **dados das três tabelas ao mesmo tempo**.



# 🔎 Conclusão da aula

DER → Modelo Relacional → SQL

Estrutura final:

Empregado (tabela física)

Projeto (tabela física)

Participar (tabela lógica)

A tabela **Participar conecta as outras duas usando chaves estrangeiras**.


