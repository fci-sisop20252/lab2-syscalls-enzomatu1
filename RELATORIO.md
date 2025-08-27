# 📝 Relatório do Laboratório 2 - Chamadas de Sistema

---

## 1️⃣ Exercício 1a - Observação printf() vs 1b - write()

### 💻 Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### 🔍 Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: 9 syscalls
- ex1b_write: 7 syscalls

**2. Por que há diferença entre os dois métodos? Consulte o docs/printf_vs_write.md**

```
O printf usa o buffer e assim pode variar a quantidade de syscalls, porém, o write sempre terá a mesma quantidade de syscalls e de chamadas.
```

**3. Qual método é mais previsível? Por quê você acha isso?**

```
write, já que a cada write usado, será exatamente um syscall
```

---

## 2️⃣ Exercício 2 - Leitura de Arquivo

### 📊 Resultados da execução:
- File descriptor: 3
- Bytes lidos: 127

### 🔧 Comando strace:
```bash
strace -e openat,read,close ./ex2_leitura
```

### 🔍 Análise

**1. Qual file descriptor foi usado? Por que não começou em 0, 1 ou 2?**

```
foi usado o 3, não se usa o 0,1 e 2 pois já estão reservados para stdin, stdout e stderr
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
pois o buffer máximo era de 128-1, e foram lidos 127.
```

**3. Por que verificar retorno de cada syscall?**

```
se verifica cada syscall pois precisamos ver onde erros estão ocorrendo.
```

---

## 3️⃣ Exercício 3 - Contador com Loop

### 📋 Resultados (BUFFER_SIZE = 64):
- Linhas: 25 (esperado: 25)
- Caracteres: 1300
- Chamadas read(): 21
- Tempo: 0.000150 segundos

### 🧪 Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |         82       |      0.000198     |
| 64          |         21       |      0.000150     |
| 256         |         6        |      0.000063     |
| 1024        |         2        |      0.000068     |

### 🔍 Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
quanto maior o buffer menos espaço ele tem, assim será necessários mais syscalls para reter toda a informação
```

**2. Todas as chamadas read() retornaram BUFFER_SIZE bytes? Discorra brevemente sobre**

```
Não, já que a última não encheu o buffer totalmente antes de devolver, já que não havia mais nada para processar.
```

**3. Qual é a relação entre syscalls e performance?**

```
quanto menos vezes o kernel precisa ser acessado, mais rápido se torna a operação
```

---

## 4️⃣ Exercício 4 - Cópia de Arquivo

### 📈 Resultados:
- Bytes copiados: 1364
- Operações: 6
- Tempo: 0.000259 segundos
- Throughput: 5142.98 KB/s

### ✅ Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [ x ] Idênticos [ ] Diferentes

### 🔍 Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Pois são dois arquivos diferentes, logo se a quantidade de bytes escritos em certo momento for maior ou menor do que a quantidade de bytes lidos, significa que o arquivo destino saiu errado.
```

**2. Que flags são essenciais no open() do destino?**

```
O_WRONLY é essencial pois sem a permissão de write, o arquivo ficaria vazio.
```

**3. O número de reads e writes é igual? Por quê?**

```
pois o write depende do read para poder escrever no arquivo, e estão sendo chamados ao mesmo tempo.
```

**4. Como você saberia se o disco ficou cheio?**

```
Sabemos que o disco ficou cheio quando write retorna -1
```

**5. O que acontece se esquecer de fechar os arquivos?**

```
O programa vaza para a memoria geral
```

---

## 🎯 Análise Geral

### 📖 Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
Syscalls transferem o controle do programa do modo usuário para o kernel para executar operações privilegiadas


```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
File descriptors identificam recursos abertos, permitindo o controle e acesso a arquivos e dispositivos

```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
Buffers maiores reduzem chamadas de sistema, melhorando desempenho, mas podem usar mais memória
```

### ⚡ Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** user

**Por que você acha que foi mais rápido?**

```
pois o user é o que passa menos tempo rodando o código
```

---

## 📤 Entrega
Certifique-se de ter:
- [X] Todos os códigos com TODOs completados
- [X] Traces salvos em `traces/`
- [X] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```
# Bom trabalho!
