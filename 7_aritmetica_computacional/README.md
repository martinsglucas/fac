[Voltar à página inicial](https://github.com/W4RT1N5/fac#glossário)
# Aritmética Computacional
* Adição e subtração
    * overflow
* Multiplicação e divisão
* Representação de ponto fluante
# Inteiros
* infinto 
    * no computador, esse conjunto é limitado pelo tamanho da memória. No caso do MIPS 32 vai de $-2^{31}$ a $2^{31}-1$ para números com sinal e de 0 a $2^{32}-1$ para números sem sinal
    * ao ultrapssar o limite, ocorre o que chamamos de *overflow*
* discreto
# Reais
* infinito
* contínuo
    * underflow: não é possível representar números muito pequenos
    * épsilon de máquina: menor número que pode ser representado ($\epsilon_{mach}$)
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
* Na **soma**, quando o sinal dos operandos for igual e o resultado for diferente
    * se somar dois positivos e der negativo ou somar dois negativos e der positivo
* Na **subtração** quando os sinais dos operandos forem diferentes
## Não há overflow
* **soma**, com sinais diferentes
* **subtrações**, com mesmo sinal

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

Obs: ($2^{32}$ - `$t2`) é o complemento de `$t2` e ($2^{32}$ - `$t2` - 1) é o `%t2` apenas negado 

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
* M: 32 bits
* P: 64 bits

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
3. Descolar P uma casa à direita
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
Calcula a multiplicação e salva o resultaod nos registradores especiais `hi` e `lo`

|hi|lo|
|:--:|:--:|
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