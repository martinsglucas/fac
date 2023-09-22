# Representação das instruções em linguagem de máquina
As instruções em assembly MIPS são traduzidas para binário e seguem 3 formatos padrão: **tipo R**, **tipo I** e **tipo J**
## Formato tipo R (3 registradores)
A instrução também tem 32 bits e é dividida em 6 campos
| op | rs | rt | rd | shamt | funct |
| :--: | :--: | :--: | :--: | :--: | :--: |
| 6 bits | 5 bits | 5 bits | 5 bits | 5 bits | 6 bits |
* op: código da operação (tipo)
* funct: código aritmético
* rs e rt: registradorres operando (em ordem)
* rd: registrador destino
* shamt: tamanho do deslocamento <!--shift amount-->

op e funct são tabeladas 
### Exemplo
<div align="center">

```asm
add $t0, $s1, $s2
```
<svg id="arrow-down-circle" xmlns="http://www.w3.org/2000/svg" width="30" height="30" fill="currentColor" class="bi bi-arrow-down-circle-fill" viewBox="0 0 16 16"><path d="M16 8A8 8 0 1 1 0 8a8 8 0 0 1 16 0zM8.5 4.5a.5.5 0 0 0-1 0v5.793L5.354 8.146a.5.5 0 1 0-.708.708l3 3a.5.5 0 0 0 .708 0l3-3a.5.5 0 0 0-.708-.708L8.5 10.293V4.5z"/></svg>

| op | rs | rt | rd | shamt | funct |
| :--: | :--: | :--: | :--: | :--: | :--: |  
| tipoR | $s1 | $s2 | $t0 | 0 | add |

<svg id="arrow-down-circle" xmlns="http://www.w3.org/2000/svg" width="30" height="30" fill="currentColor" class="bi bi-arrow-down-circle-fill" viewBox="0 0 16 16"><path d="M16 8A8 8 0 1 1 0 8a8 8 0 0 1 16 0zM8.5 4.5a.5.5 0 0 0-1 0v5.793L5.354 8.146a.5.5 0 1 0-.708.708l3 3a.5.5 0 0 0 .708 0l3-3a.5.5 0 0 0-.708-.708L8.5 10.293V4.5z"/></svg>

| o | 17 | 18 | 8 | 0 | 32 |  
| :--: | :--: | :--: | :--: | :--: | :--: |

<svg id="arrow-down-circle" xmlns="http://www.w3.org/2000/svg" width="30" height="30" fill="currentColor" class="bi bi-arrow-down-circle-fill" viewBox="0 0 16 16"><path d="M16 8A8 8 0 1 1 0 8a8 8 0 0 1 16 0zM8.5 4.5a.5.5 0 0 0-1 0v5.793L5.354 8.146a.5.5 0 1 0-.708.708l3 3a.5.5 0 0 0 .708 0l3-3a.5.5 0 0 0-.708-.708L8.5 10.293V4.5z"/></svg>

binário de 32 bits
</div>

## Formato tipo I (2 registradores e constante)
| op | rs | rt | constante/endereço | 
| :--: | :--: | :--: | :--: |  
| 6 bits | 5 bits | 5 bits | 16 bits

### Exemplo
<div align="center">

```asm
addi $t0, $s1, 21
```
<svg id="arrow-down-circle" xmlns="http://www.w3.org/2000/svg" width="30" height="30" fill="currentColor" class="bi bi-arrow-down-circle-fill" viewBox="0 0 16 16"><path d="M16 8A8 8 0 1 1 0 8a8 8 0 0 1 16 0zM8.5 4.5a.5.5 0 0 0-1 0v5.793L5.354 8.146a.5.5 0 1 0-.708.708l3 3a.5.5 0 0 0 .708 0l3-3a.5.5 0 0 0-.708-.708L8.5 10.293V4.5z"/></svg>

| op | rs | rt | constante/endereço | 
| :--: | :--: | :--: | :--: |  
| tipoI | $t0 | $s0 | 21

<svg id="arrow-down-circle" xmlns="http://www.w3.org/2000/svg" width="30" height="30" fill="currentColor" class="bi bi-arrow-down-circle-fill" viewBox="0 0 16 16"><path d="M16 8A8 8 0 1 1 0 8a8 8 0 0 1 16 0zM8.5 4.5a.5.5 0 0 0-1 0v5.793L5.354 8.146a.5.5 0 1 0-.708.708l3 3a.5.5 0 0 0 .708 0l3-3a.5.5 0 0 0-.708-.708L8.5 10.293V4.5z"/></svg>

| 4 | 8 | 16 | 21 |
| :--: | :--: | :--: | :--: |

<svg id="arrow-down-circle" xmlns="http://www.w3.org/2000/svg" width="30" height="30" fill="currentColor" class="bi bi-arrow-down-circle-fill" viewBox="0 0 16 16"><path d="M16 8A8 8 0 1 1 0 8a8 8 0 0 1 16 0zM8.5 4.5a.5.5 0 0 0-1 0v5.793L5.354 8.146a.5.5 0 1 0-.708.708l3 3a.5.5 0 0 0 .708 0l3-3a.5.5 0 0 0-.708-.708L8.5 10.293V4.5z"/></svg>

binário de 32 bits
</div>

#### Observação
No tipo I, uma constante varia de $-2^{15}$ a $2^{15}-1$  
### Operações imediatas
Para operações imediatas, se precisar de uma constante maior, deve-se salvar na memória (.data) e carregar em um registrador usnado `lw`

Para `sw` e `lw`, explica por que a lógica é end. base + offset

<svg id="arrow-down-circle" xmlns="http://www.w3.org/2000/svg" width="30" height="30" fill="currentColor" class="bi bi-arrow-down-circle-fill" viewBox="0 0 16 16"><path d="M16 8A8 8 0 1 1 0 8a8 8 0 0 1 16 0zM8.5 4.5a.5.5 0 0 0-1 0v5.793L5.354 8.146a.5.5 0 1 0-.708.708l3 3a.5.5 0 0 0 .708 0l3-3a.5.5 0 0 0-.708-.708L8.5 10.293V4.5z"/></svg>


# Instruções lógicas de deslocamento
## Deslocamentos (tipo R)
* esquerda: `sll $t0, $s0, 4` (mnemônico shift left logical) (o 4 é shamt - uma constante apesar de ser do tipo R)  
*Obs: o 4 é uma constante (é o shamt), apesar de ser do tipo R*  
*Obs2: $t0 é o rd, $s0 é o rs, o rt é desconsiderado*
* direita: `srl $st0, $s0, 10` (mnemônico shift right logical)
## Lógicas
### AND
Faz o E lógico bit a bit entre `$s0` e `$s1` e salva em `$t0`
```asm
and $t0, $s0, $s1
```
### OR
Faz o OU lógico bit a bit
```asm
or $t0, $s0, $s1
```
### NOR
OU lógico bit a bit e salva o resultado negado
```asm
nor $t0, $s0, $zero
```
### XOR
XOR lógico bit a bit
```asm
xor $t0, $s0, $s1
```

