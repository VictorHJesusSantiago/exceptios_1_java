<div align="center">

<img src="https://cdn-icons-png.flaticon.com/512/2989/2989898.png" alt="Exceptions Java Logo" width="110" />

# ⚠️ Exceptions 1 — Tratamento de Exceções em Java

**Projeto prático de tratamento de exceções em Java com sistema de reservas de hotel,**
**demonstrando exceções customizadas, hierarquia de catches e validações de domínio.**

<br>

![Java](https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![NetBeans](https://img.shields.io/badge/Apache%20NetBeans-1B6AC6?style=for-the-badge&logo=apache-netbeans-ide&logoColor=white)
![OOP](https://img.shields.io/badge/Paradigma-OOP-blueviolet?style=for-the-badge)
![Console](https://img.shields.io/badge/Interface-Console-555555?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completo-brightgreen?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)

</div>

---

## 📚 Tabela de Conteúdos

> Navegue rapidamente pelas seções do projeto.

| # | Seção |
|:-:|:------|
| 1 | [📖 Sobre o Projeto](#-sobre-o-projeto) |
| 2 | [🎯 Conceitos de Exceções Aplicados](#-conceitos-de-exceções-aplicados) |
| 3 | [🔑 Arquitetura e Classes](#-arquitetura-e-classes) |
| 4 | [⚙️ Regras de Negócio e Validações](#️-regras-de-negócio-e-validações) |
| 5 | [🛠️ Pilha de Tecnologias](#️-pilha-de-tecnologias) |
| 6 | [📂 Estrutura do Projeto](#-estrutura-do-projeto) |
| 7 | [🚀 Como Executar](#-como-executar) |
| 8 | [🖥️ Exemplos de Execução](#️-exemplos-de-execução) |
| 9 | [🤝 Como Contribuir](#-como-contribuir) |
| 10 | [👨‍💻 Autor](#-autor) |
| 11 | [📄 Licença](#-licença) |

---

## 📖 Sobre o Projeto

> **Exceptions 1** é um projeto prático que demonstra o uso correto de **tratamento de exceções em Java** através de um sistema de gerenciamento de reservas de hotel.

O projeto implementa uma **exceção customizada de domínio** (`DomainException`) que é lançada pela entidade `Reservation` quando as regras de negócio são violadas — como datas inválidas ou tentativas de atualizar reservas com datas passadas. O `Program.java` demonstra uma cadeia de `try-catch` hierárquica e bem organizada.

---

## 🎯 Conceitos de Exceções Aplicados

| Conceito | Implementação no Projeto |
|:---------|:------------------------|
| 🔴 **Exceção Customizada** | `DomainException extends RuntimeException` — encapsula erros de regra de negócio. |
| 🔵 **Checked Exception** | `ParseException` — capturada ao converter `String` para `Date` com `SimpleDateFormat`. |
| 🟡 **Unchecked Exception** | `RuntimeException` — capturada como fallback para erros inesperados. |
| 📦 **Cláusula `throws`** | Construtor e método `updateDates()` de `Reservation` declaram `throws DomainException`. |
| 🔗 **Cadeia de `catch`** | Hierarquia correta: `ParseException` → `DomainException` → `RuntimeException`. |
| 🏭 **Lançamento com `throw`** | `throw new DomainException(...)` disparado diretamente pela entidade ao detectar violação. |
| 🧬 **`serialVersionUID`** | Definido em `DomainException` conforme boas práticas para classes `Serializable`. |

---

## 🔑 Arquitetura e Classes

### 📐 Diagrama de Classes

```
┌─────────────────────────────────────────────────────────┐
│                      Program.java                       │
│  main() → Scanner → cria Reservation → updateDates()   │
│                                                         │
│  try {                                                  │
│    ...                                                  │
│  } catch (ParseException e) { ... }                     │
│  } catch (DomainException e) { ... }   ←── captura aqui │
│  } catch (RuntimeException e) { ... }                   │
└────────────────────────┬────────────────────────────────┘
                         │ usa
┌────────────────────────▼────────────────────────────────┐
│              model.entities.Reservation                 │
│                                                         │
│  - roomNumber : Integer                                 │
│  - checkIn    : Date                                    │
│  - checkOut   : Date                                    │
│                                                         │
│  + Reservation(num, in, out) throws DomainException     │
│  + updateDates(in, out)      throws DomainException ────┼──┐
│  + duration() : long                                    │  │
│  + toString() : String                                  │  │ lança
└─────────────────────────────────────────────────────────┘  │
                                                             │
┌────────────────────────────────────────────────────────────▼┐
│           model.exceptions.DomainException                  │
│                                                             │
│  extends RuntimeException                                   │
│  + DomainException(String msg) → super(msg)                 │
└─────────────────────────────────────────────────────────────┘
```

### 📋 Descrição das Classes

| Classe | Pacote | Responsabilidade |
|:-------|:------:|:-----------------|
| `Program.java` | *(default)* | Ponto de entrada — coleta dados via `Scanner`, instancia `Reservation` e trata exceções com `try-catch` em cadeia. |
| `Reservation.java` | `model.entities` | Entidade de reserva — valida datas no construtor e no `updateDates()`, lançando `DomainException` ao detectar violações. |
| `DomainException.java` | `model.exceptions` | Exceção customizada de domínio — estende `RuntimeException` para sinalizar violações de regra de negócio. |

---

## ⚙️ Regras de Negócio e Validações

> Todas as validações são realizadas **dentro da própria entidade** `Reservation`, seguindo o princípio de encapsulamento e mantendo a lógica de negócio no lugar correto.

### ✅ Validações no Construtor `Reservation()`

| Regra | Condição Verificada | Exceção Lançada |
|:------|:-------------------:|:---------------:|
| Check-out deve ser **após** o check-in | `!checkOut.after(checkIn)` | `DomainException("Check-out date must be after check-in date.")` |

### ✅ Validações no Método `updateDates()`

| Regra | Condição Verificada | Exceção Lançada |
|:------|:-------------------:|:---------------:|
| Datas devem ser **futuras** | `checkIn.before(now) \|\| checkOut.before(now)` | `DomainException("Reservation dates for update must be future dates.")` |
| Check-out deve ser **após** o check-in | `!checkOut.after(checkIn)` | `DomainException("Check-out date must be after check-in date.")` |

---

### 🔗 Cadeia de `catch` em `Program.java`

```java
try {
    // Criação e atualização da reserva
    Reservation reservation = new Reservation(number, checkIn, checkOut);
    reservation.updateDates(checkIn, checkOut);

} catch (ParseException e) {
    // Captura erro de formato de data inválido (ex: "32/13/2025")
    System.out.println("Invalid date format");

} catch (DomainException e) {
    // Captura violações de regra de negócio lançadas pela entidade
    System.out.println("Error in reservation: " + e.getMessage());

} catch (RuntimeException e) {
    // Fallback para qualquer erro inesperado não previsto
    System.out.println("Unexpected Error.");
}
```

> ⚠️ **A ordem dos `catch` importa!** `DomainException` deve vir antes de `RuntimeException` pois é sua subclasse — invertê-los causaria um erro de compilação.

---

## 🛠️ Pilha de Tecnologias

| Tecnologia | Função no Projeto |
|:-----------|:------------------|
| **Java** | Linguagem principal — toda a lógica de domínio e tratamento de exceções. |
| **`java.util.Scanner`** | Leitura de dados do usuário via console. |
| **`java.text.SimpleDateFormat`** | Conversão de `String` para `Date` no formato `dd/MM/yyyy`. |
| **`java.util.concurrent.TimeUnit`** | Cálculo da diferença em dias entre check-in e check-out. |
| **Apache NetBeans** | IDE utilizada no desenvolvimento (`nbproject/` e `build.xml` incluídos). |
| **Apache Ant** | Sistema de build via `build.xml` utilizado pelo NetBeans. |

---

## 📂 Estrutura do Projeto

```plaintext
exceptios_1_java-main/
│
├── 📄 build.xml                              # ⚙️  Arquivo de build (Apache Ant)
├── 📄 manifest.mf                            # 📋 Manifesto da aplicação
├── 📄 .gitignore                             # 🚫 Arquivos ignorados pelo Git
│
├── 📁 nbproject/                             # ⚙️  Configurações do Apache NetBeans
│   ├── 📄 build-impl.xml
│   ├── 📄 genfiles.properties
│   ├── 📄 project.properties
│   ├── 📄 project.xml
│   └── 📁 private/
│       ├── 📄 private.properties
│       └── 📄 private.xml
│
└── 📁 src/
    ├── 📄 Program.java                       # ▶️  Ponto de entrada — try-catch em cadeia ← CORE
    │
    └── 📁 model/
        ├── 📁 entities/
        │   └── 📄 Reservation.java           # 🏛️  Entidade com validações e lança DomainException ← CORE
        │
        └── 📁 exceptions/
            └── 📄 DomainException.java       # 🚨 Exceção customizada de domínio ← CORE
```

---

## 🚀 Como Executar

### 📋 Pré-requisitos

| Requisito | Detalhe |
|:----------|:--------|
| **JDK** | Versão **8 ou superior** instalada e configurada no `PATH`. |
| **Apache NetBeans** | *(Recomendado)* IDE utilizada no desenvolvimento original. |
| **Git** | Para clonar o repositório. |

---

### 🔧 Opção 1 — Apache NetBeans (Recomendado)

```
1. Abra o Apache NetBeans IDE
2. File → Open Project...
3. Selecione a pasta 'exceptios_1_java-main'
4. Clique em "Run Project" ou pressione F6
```

---

### 💻 Opção 2 — Terminal (Linha de Comando)

**1. Clone o repositório:**

```bash
git clone https://github.com/VictorHJesusSantiago/exceptios_1_java.git
cd exceptios_1_java/src
```

**2. Compile todos os arquivos:**

```bash
javac -d . model/exceptions/DomainException.java model/entities/Reservation.java Program.java
```

**3. Execute o programa:**

```bash
java Program
```

---

## 🖥️ Exemplos de Execução

### ✅ Cenário 1 — Reserva Válida

```
Room number:
101
Check-in date (dd/MM/yyyy):
25/12/2025
Check-out date (dd/MM/yyyy):
28/12/2025
Reservation: Room 101, check-in: 25/12/2025, check-out: 28/12/2025, 3 nights

Enter data to update the reservation:
Check-in date (dd/MM/yyyy):
26/12/2025
Check-out date (dd/MM/yyyy):
29/12/2025
Reservation: Room 101, check-in: 26/12/2025, check-out: 29/12/2025, 3 nights
```

---

### ❌ Cenário 2 — Check-out antes do Check-in

```
Room number:
202
Check-in date (dd/MM/yyyy):
28/12/2025
Check-out date (dd/MM/yyyy):
25/12/2025
Error in reservation: Check-out date must be after check-in date.
```

---

### ❌ Cenário 3 — Atualização com Datas Passadas

```
Room number:
303
Check-in date (dd/MM/yyyy):
25/12/2025
Check-out date (dd/MM/yyyy):
28/12/2025
Reservation: Room 303, check-in: 25/12/2025, check-out: 28/12/2025, 3 nights

Enter data to update the reservation:
Check-in date (dd/MM/yyyy):
01/01/2020
Check-out date (dd/MM/yyyy):
05/01/2020
Error in reservation: Reservation dates for update must be future dates.
```

---

### ❌ Cenário 4 — Formato de Data Inválido

```
Room number:
404
Check-in date (dd/MM/yyyy):
data_invalida
Invalid date format
```

---

## 🤝 Como Contribuir

> Contribuições são muito bem-vindas! Siga as etapas abaixo para colaborar de forma organizada.

| Passo | Ação | Comando |
|:-----:|:-----|:--------|
| 1️⃣ | **Fork** | Crie um fork do repositório para a sua conta. | — |
| 2️⃣ | **Branch** | Crie sua feature branch a partir da `main`. | `git checkout -b feature/NovaFeature` |
| 3️⃣ | **Commit** | Salve as alterações com mensagem clara e semântica. | `git commit -m 'feat: Adiciona NovaFeature'` |
| 4️⃣ | **Push** | Envie a branch para o repositório remoto. | `git push origin feature/NovaFeature` |
| 5️⃣ | **Pull Request** | Abra um PR detalhando as mudanças realizadas. | — |

<div align="center">

<br>

**Se este projeto foi útil para os seus estudos, deixe uma estrela ⭐️ no repositório!**

</div>

---

## 👨‍💻 Autor

<div align="center">

<br>

**Victor H. J. Santiago**

<br>

[![GitHub](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)](https://github.com/VictorHJesusSantiago)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/victor-henrique-de-jesus-santiago/)

</div>

---

## 📄 Licença

<div align="center">

Este projeto está distribuído sob a **Licença MIT**.
Consulte o arquivo [`LICENSE`](./LICENSE) no repositório para mais informações.

![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)

</div>

---

<div align="center">

*Feito com ⚠️ e Java por **Victor H. J. Santiago***

</div>
