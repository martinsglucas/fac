[Voltar à página inicial](https://github.com/martinsglucas/fac#sumário)

# Aritmética Computacional

- Adição e subtração
  - overflow
- Multiplicação e divisão
- Representação de ponto fluante

# Inteiros

- infinto
  - no computador, esse conjunto é limitado pelo tamanho da memória. No caso do MIPS 32 vai de $-2^{31}$ a $2^{31}-1$ para números com sinal e de 0 a $2^{32}-1$ para números sem sinal
  - ao ultrapssar o limite, ocorre o que chamamos de _overflow_
- discreto

# Reais

- infinito
- contínuo
  - underflow: não é possível representar números muito pequenos
  - épsilon de máquina: menor número que pode ser representado ($\epsilon_{mach}$)

# Adição

<table>
    <tr>
        <td>carry</td>
        <td>1</td>
        <td>1</td>
    </tr>
    <tr>
        <td rowspan="2">+</td>
        <td>0</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
    </tr>
    <tr>
        <td>0</td>
        <td>1</td>
        <td>1</td>
        <td>0</td>
    </tr>
    <tr>
        <td>=</td>
        <td>1</td>
        <td>1</td>
        <td>0</td>
        <td>1</td>
    </tr>
</table>
Nesse exemplo, o número for sem sinal, ok. Mas se for o número com sinal, overflow

## Overflow pode acontecer

- Na **soma**, quando o sinal dos operandos for igual e o resultado for diferente
  - se somar dois positivos e der negativo ou somar dois negativos e der positivo
- Na **subtração** quando os sinais dos operandos forem diferentes

## Não há overflow

- **soma**, com sinais diferentes
- **subtrações**, com mesmo sinal

## Exemplo: verificar overflow

Código do livro

```asm
# vamos começar verificando se não houve overflow
addu $t0, $t1, $t2 # addu não lança exceção
xor $t3, $t1, $t2 # se o sinal(t1)!=(t2) t3 < 0, nesse caso ele ve se o numero gerado é negativo
slt $t3, $t3, $zero # se t3 < 0, t3 = 1, entao não houve overflow
bne $t3, $zero, sem-overflow
# se o sinal dos operandos for igual e o resultado for diferente, houve overflow
xor $t3, $t0, $t1 # se o sinal(t0)!=(t1) t3 < 0
slt $t3, $t3, $zero
bne $t3, $zero, overflow
```

## Exemplo para números sem sinal

`$t1` + `$t2` > $2^{32}-1$ ?

`$t1` > $2^{32}$ - `$t2` - 1 ?

Obs: ($2^{32}$ - `$t2`) é o complemento de `$t2` e ($2^{32}$ - `$t2` - 1) é o `$t2` apenas negado

```asm
addu $t0, $t1, $t2
nor $t3, $t2, $zero # t3 = 2^32 - $t2 - 1
slt $t3, $t3, $t1 # 2^32 - t2 - 1 < t1 ?
bne $t3, $zero, overflow
```

# Multiplicação de inteiros

<table>
    <tr>
        <td></td>
        <td></td>
        <td></td>
        <td rowspan="2">X</td>
        <td>1</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
        <td>1</td>
        <td>0</td>
        <td>0</td>
        <td>1</td>
    </tr>
    <tr>
        <td rowspan="4">+</td>
        <td></td>
        <td></td>
        <td></td>
        <td>1</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td></td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>0</td>
        <td>0</td>
        <td>1</td>
    </tr>
    <tr>
        <td>=</td>
        <td>1</td>
        <td>0</td>
        <td>0</td>
        <td>1</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
    </tr>
</table>

## Algoritmo

M: multiplicando, Q: multiplicador, P: produto

1. P = 0
2. Se Q[0] = 1 (o bit menos significativo do multiplicador), então P = P + M
   - Q[0] seria um andi Q,Q,1
3. Faça um deslocamento à esquerda em M
4. Faça um deslocamento à direita em Q
5. Se não for a n-ésima (qtde de bits) iteração, voltar ao passo 2

## Exemplo

<table>
    <tr>
        <td>Iteração</td>
        <td>M</td>
        <td>Q</td>
        <td>P</td>
    </tr>
    <tr>
        <td>0</td>
        <td>1000</td>
        <td>1001</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10000</td>
        <td>100</td>
        <td>1000</td>
    </tr>
    <tr>
        <td>2</td>
        <td>100000</td>
        <td>10</td>
        <td>1000</td>
    </tr>
    <tr>
        <td>3</td>
        <td>1000000</td>
        <td>1</td>
        <td>1000</td>
    </tr>
    <tr>
        <td>4</td>
        <td>10000000</td>
        <td>0</td>
        <td>1001000</td>
    </tr>
</table>

Para executar esse algoritmo, precisamos de:

- M: 64 bits
- Q: 32 bits
- P: 64 bits

Outra possibilidade é deslocar somente o produto à direita. Nesse caso, precisamos de:

- M: 32 bits
- Q: 32 bits
- P: 64 bits

Como o Q é deslocado à direita a cada iteração. Para melhorar, é possível usar o registrador dos bits menos significativos de P para armazenar o Q. Nesse caso, precisamos de:

- M: 32 bits
- P: 64 bits

<table>
    <tr>
        <td></td>
        <td>1</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td></td>
        <td>1</td>
        <td>0</td>
        <td>0</td>
        <td>1</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td>iteracao 1</td>
        <td>1</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>iteracao 2</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>iteracao 3</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>iteracao 4</td>
        <td>1</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
    </tr>
</table>

<div><table>
    <tr>
        <td>reg 1</td>
        <td>reg 2</td>
    </tr>
    <tr>
        <td>1000</td>
        <td>Q</td>
    </tr>
    <tr>
        <td>0100</td>
        <td>0Q</td>
    </tr>
    <tr>
        <td>0010</td>
        <td>00Q</td>
    </tr>
    <tr>
        <td>1001</td>
        <td>000Q</td>
    </tr>
    <tr>
        <td>1001</td>
        <td>0000</td>
    </tr>
</table></div>

## Algoritmo Otimizado

M: multiplicando, Q: multiplicador, P: produto

1. P[63...32] = 0 e P[31...0] = Q
2. Se P[0] = 1, então P[63...32] += M
3. Deslocar P uma casa à direita
4. Se não for a 32ª iteração, voltar ao passo 2

### Multiplicar 2 x 3: 0010 x 0011

M: 0010 e Q: 0011

<div><table>
    <tr>
        <td>IT</td>
        <td>Etapa</td>
        <td>Produto</td>
    </tr>
    <tr>
        <td>0</td>
        <td>valores iniciais</td>
        <td>0000 001<b>1</b></td>
    </tr>
    <tr>
        <td>1</td>
        <td>P[7...4] + M<br>srl P,P,1</td>
        <td>0010 0011 <br> 0001 000<b>1</b></td>
    </tr>
    <tr>
        <td>2</td>
        <td>P[7...4] + M<br>srl P,P,1</td>
        <td>0011 0001<br>0001 100<b>0</b></td>
    </tr>
    <tr>
        <td>3</td>
        <td>Mantém P<br>srl P,P,1</td>
        <td>0001 1000<br>0000 110<b>0</b></td>
    </tr>
    <tr>
        <td>4</td>
        <td>Mantém P<br>srl P,P,1</td>
        <td>0000 1100<br>0000 <b>0110</b></td>
    </tr>
</table></div>

### Observação

O algoritmo apresentado funciona apenas para **inteiros sem sinal**. Para multiplicar números com sinal, basta converter os operandos para positivo, executar o algoritmo e aplicar regra de sinal ao final.

## Instruções no Assembly MIPS
Calcula a multiplicação e salva o resultado nos registradores especiais `hi` e `lo`

|   hi    |   lo    |
| :-----: | :-----: |
| 32 bits | 32 bits |

```asm
mult r1,r2
multu r1,r2
```

Para acessar o conteúdo desses registradores

```asm
# move from
mfhi reg # copia o conteúdo de hi para reg
mflo reg # copia o conteúdo de lo para reg
# move to
mthi reg # copia o conteúdo de reg para hi
mtlo reg # copia o conteúdo de reg para lo
```

## Observação

`mul rd, rs, rt` executa `rs` x `rt` e salva em `rd`. Funciona bem se `rs` e `rt` tiverem no máximo 16 bits

# Divisão de inteiros

1001010 / 1000 = 1001, resto 010

dividendo = 1001010, divisor = 1000
<div>
<table>
    <tr>
        <td></td>
        <td>1001010</td>
    </tr>
        <td>1000</td>
        <td>0000000</td>
</table>
dividendo - divisor < 0
<table>
    <tr>
        <td></td>
        <td>1001010</td>
    </tr>
        <td>0100</td>
        <td>0000000</td>
</table>
dividendo - divisor < 0
<table>
    <tr>
        <td></td>
        <td>1001010</td>
    </tr>
        <td>0010</td>
        <td>0000000</td>
</table>
dividendo - divisor < 0
<table>
    <tr>
        <td></td>
        <td>1001010</td>
    </tr>
        <td>0001</td>
        <td>0000000</td>
</table>
dividendo - divisor < 0
<table>
    <tr>
        <td></td>
        <td>1001010</td>
    </tr>
        <td>0000</td>
        <td>1000000</td>
</table>
dividendo - divisor > 0
então o dividendo = dividendo - divisor e o quociente = 1
<table>
    <tr>
        <td></td>
        <td>0001010</td>
    </tr>
        <td>0000</td>
        <td>0100000</td>
</table>
dividendo - divisor < 0 e o quociente = 10
<table>
    <tr>
        <td></td>
        <td>0001010</td>
    </tr>
        <td>0000</td>
        <td>0010000</td>
</table>
dividendo - divisor < 0 e o quociente = 100
<table>
    <tr>
        <td></td>
        <td>0001010</td>
    </tr>
        <td>0000</td>
        <td>0001000</td>
</table>
dividendo - divisor > 0 então o dividendo = dividendo - divisor e o quociente = 1001
<table>
    <tr>
        <td></td>
        <td>0000010</td>
    </tr>
        <td>0000</td>
        <td>0001000</td>
</table>
</div>

## Algoritmo

Dividendo e quociente: 32 bits
Divisor e resto: 64 bits
 1. Resto = Dividendo. Salve o divisor na parte mais significativa do registrador
 2. Resto = Resto - Divisor
 3. Desloque o quociente 1 bit à esquerda
    - se resto < 0, restaure o resto e defina Q[0] = 0
    - se resto >= 0, defina Q[0] = 1
4. Desloque o divisor 1 bit à direita. Se a iteração não for a 33ª, volte ao passo 2

### Exemplo: 7 / 2

| it | Etapa | Quociente | Divisor | Resto |
| :--: | :--: | :--: | :--: | :--: |
0 | Valores iniciais | 0000 | 0010 $\color{red}0000$ | 0000 0111
1 | Resto -= Divisor <br> Passos 3 e 4 | 0000 <br> 0000 | 0010 $\color{red}0000$ <br> 0001 $\color{red}0000$ | < 0 <br> 0000 0111 |
2 | Resto -= Divisor <br> Passos 3 e 4 | 0000 <br> 0000 | ${\color{red}0}001$ $0\color{red}000$ <br> ${\color{red}00}00$ $10\color{red}00$ | < 0 <br> 0000 0111 |
3 | Resto -= Divisor <br> Passos 3 e 4 | 0000 <br> 0000 | ${\color{red}00}00$ $10\color{red}00$ <br> ${\color{red}000}0$ $010\color{red}0$ | < 0 <br> 0000 0111 |
4 | Resto -= Divisor <br> Passos 3 e 4 | 0000 <br> 0001 | ${\color{red}000}0$ $010\color{red}0$ <br> $\color{red}0000$ 0010 | 0000 0011 <br> 0000 0011 |
5 | Resto -= Divisor <br> Passos 3 e 4 | 0001 <br> <b>0011</b> | 0000 0010 <br> 0000 0001 | 0000 0001 <br> <b>0000 0001</b> |

Nessa solução, metade dos bits dos registradores serão nulos

### Agora deslocando o Resto

Divisor: 0010  
Resto: 0000 | 0111  
Resto: 0000 | 1110  
Resto: 0000 | 1110  
Resto: 0001 | 1100  
Resto: 0011 | 1000 aqui a subtração dá positiva, então  
Resto: 0001 | 1001  
Resto: 0011 | 0010 aqui a subtração dá positiva, então  
Resto: **0001** | **0011**  

## Algoritmo Otimizado
1. Resto = Dividendo. i = 1
2. Desloque o resto 1 bit à esquerda
3. Resto[63...32] -= Divisor
    - Se o resto < 0, restaure o valor do resto e desloque e desloque 1 bit à esquerda
    Se o resto >= 0, desloque o resto 1 bit à esquerda e defina Resto[0] = 1
4. Se i <= 32, i++ e volte ao passo 3
5. Desloque Resto[63...32] 1 bit à direita

### Mesmo exemplo: 7 / 2

| it | Etapa | Resto | Divisor |
| :--: | :--: | :--: | :--: |
| 0 | Passos 1 e 2 | 0000 1110 | 0000 0010 |
| 1 | Resto[7...4] -= Divisor <br> Passos 3 e 4 | <0 1110 <br> 0001 1100 |
| 2 | Resto[7...4] -= Divisor <br> Passos 3 e 4 | <0 1100 <br> 0011 1000 |
| 3 | Resto[7...4] -= Divisor <br> Passos 3 e 4 | 0001 1000 <br> 0011 0001 |
| 4 | Resto[7...4] -= Divisor <br> Passos 3 e 4 | 0001 0001 <br> 0010 0011 |
| 5 | srl Resto[7...4] | 0001 0011 |

Então o resto é 0001 e o quociente é 0011

$\color{red}\text{Repetir esses exemplos com outros valores para fixar}$

**Esse algoritmo funciona apenas para números positivos**

Ex:
- -7 / 2 = -3, resto -1
- 7 / -2 = -3, resto 1
- -7 / -2 = 3, resto -1

O resto sempre tem o mesmo sinal do dividendo

**Portanto, para calcular a divisão entre números negativos**

- Transforme todos em positivos e execute o algoritmo
- Negue o quociente se os sinais dos operandos eram diferentes
- Faça com que o resto tenha o mesmo sinal do dividendo

## Instruções
```asm
div reg1, reg2
divu reg1, reg2
``` 
Executa reg1 / reg2 e salva o quociente em `lo` e o resto em `hi`

Use `mflo` e `mfhi` para acessar os registradores