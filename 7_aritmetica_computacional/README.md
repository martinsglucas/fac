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
        <td></td>
        <td></td>
        <td>1</td>
        <td>1</td>
    </tr>
    <tr>
        <td rowspan="2">+<td>
        <td>0</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
    </tr>
    <tr>
        <td></td>
        <td>0</td>
        <td>1</td>
        <td>1</td>
        <td>0</td>
    </tr>
    <tr>
        <td></td>
        <td>=</td>
        <td>1</td>
        <td>1</td>
        <td>0</td>
        <td>1</td>
    </tr>
</table>
Nesse exemplo, o número for sem sinal, ok. Mas se for o número com sinal, overflow

# Overflow pode acontecer
* Na **soma**, quando o sinal dos operandos for igual e o resultado for diferente
    * se somar dois positivos e der negativo ou somar dois negativos e der positivo
* Na **subtração** quando os sinais dos operandos forem diferentes
# Não há overflow
* **soma**, com sinais diferentes
* **subtrações**, com mesmo sinal

# Exemplo: verificar overflow
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
# Exemplo para números sem sinal
`$t1` + `$t2` > $2^{32}-1$ ?

`$t1` > $2^{32}$ - `$t2` - 1 ?

Obs: ($2^{32}$ - `$t2`) é o complemento de `$t2` e ($2^{32}$ - `$t2` - 1) é o `%t2` apenas negado 

```asm
addu $t0, $t1, $t2
nor $t3, $t2, $zero # t3 = 2^32 - $t2 - 1
slt $t3, $t3, $t1 # 2^32 - t2 - 1 < t1 ?
bne $t3, $zero, overflow
```