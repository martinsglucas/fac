[Voltar à página inicial](https://github.com/W4RT1N5/fac#glossário)
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
## Aplicações
### Condições compostas
Combinações de condições podem ser feitas com `and` e `or`
### Máscaras
Máscaras são operações para lidar com bits específicos de uma palavra
#### Exemplo: Extrair o bit menos significativo de `$t0`
$t0 = 00110101010...01101  
$t9 = 00000000000...00001  (mascara)  
AND = 00000000000...00001
```asm
and $t2, $t0, $t9
```
# Instruções condicionais
```asm
slt reg, r1, r2 # set on less than
```
Se r1 < r2, reg = 1, senão reg = 0  
Também existe a instrução `slti reg,r1,const` e a instrução `sltu reg,r1,r2` (unsigned)  
A `sltu` pode retornar valores diferentes de `slt` para números negativos
# Instruções de desvio condicional
```asm
beq r1, r2, label # branch if equal
# Instrução do tipo I
```
Se r1 == r2, desvia o fluxo para o linha rotulada por label
```asm
bne r1, r2, label # branch if not equal
# instrução do tipo I
```
Se r1 != r2, desvia o fluxo para o linha rotulada por label

>r1 e r2 são registradores, obrigatoriamente. Se for preciso comparar com uma constante, deve-se carregar a constante em um registrador e comparar com ele

# Como fazer para
## desviar para label se r1 => r2
O complemento de maior ou igual é menor. Então podemos fazer
```asm
slt $t0, r1, r2
beq $t0, $zero, label
# se $t0 = 0, então r1 não é menor que r2. Portanto, r1 >= r2
# essa é a pseudoinstrução bge
```
## desviar para label se r1 > r2
```asm
slt $t0, r2, r1
# para nao precisar carregar um reg = 1 e usar beq, usa-se bne
bne $t0, $zero, label
# se $t0 = 1, então r2 é menor que r1, e, então, r1 é maior que r2
# essa é a pseudoinstrução bgt
```
# Desvio incondicional
```asm
j label # jump
# instrução do tipo J
```
Desvia o fluxo para o label

# Observações
## Deslocamento relativo ao PC
label se transforma na quantidade de instruções a partir da instrução que está sendo executada

| PC = 1000 | `beq ..., label` |
| :--: | :--: |
| 1004 | |
| 1008 | |
| 1012 | |
| 1016 | |
| 1020 | label |

Logo, label = 5
O endereço de destino do desvio = 4 * label + PC  
O PC guarda o endereço da instrução que está sendo executada no momento
# Exemplo if e else
```c
if(i==j) f = g + h;
else f = g - h;
```
> f -> `$s0`, g -> `$s1`, h -> ``$s2``, i -> `$s3`, j -> `$s4`
```asm
main: beq $s3, $s4, then # if(i==j) f = g + h;
      sub $s0, $s1, $s2 # f = g - h
      j fim
if:   add $s0, $s1, $s2 # f = g + h
fim:
```
# Exemplo: Laços
```c
i = 0;
while (i < n){
    A[i] = 0;
    i++;
}
```
> i -> ``$t0``, n -> ``$s0``, A -> ``$s1``
```asm
main:   add $t0, $zero, $zero # ou move, $t0, $zero
loop:   slt $t2, $t0, $s0 # i < n ?, se sim, $t2 = 1, senão $t2 = 0
        beq $t2, $zero, exit # se i >= n, sai do loop
        sll $t1, $t0, 2 # $t1 = 4 * i
        add $t1, $t1, $s1 # $t1 = end de A[i]
        sw $zero, 0($t1) # A[i] = 0
        addi $t0, $t0, 1 # i = i + 1
        # como o i é multiplicado por 4, só é preciso somar 1
        j loop
exit:
```
# Procedimentos (funções)
Procedimento é uma nome mais geral para funções que não necessariamente retornam um valor. A função (que retorna valor) é um tipo especial de procedimento
```c
int media(int a, int b){
    return (a+b)/2;
    # aqui teria um j label
}
int main(){
    ...
    media(a,b);
    # aqui seria o label
    ...
    return 0;
}
```
## Fluxo de chamada a procedimentos
1. Coloque os argumentos nos registradores
2. Desvie a execução para o procedimento
3. Ajuste o armazenamento no procedimento
4. Execute o procedimento
5. Armazene o resultado nos registradores
6. Ajuste o armazenamento
7. Retorne ao procedimento chamador
### Registradores
* `$a0, $a1, $a2, $a3`: argumentos
* ``$v0, $v1``: retorno de procedimento
### Ajuste de Armazenamento
* ``$s0 a $s7`` devem ser preservados
Após a execução do procedimento, os valores desses registradores devem ser 'recuperados'

Se os 4 registradores não forem suficientes, pode-se usar a pilha (heap). O `$sp` (stack pointer) aponta para o topo da pilha. O `$sp` é decrementado quando um valor é empilhado e incrementado quando um valor é desempilhado.

<table>
  <tr>
    <td>Pilhas</td>
    <td rowspan="2">Heap</td>
  </tr>
  <tr>
    <td>Dados Dinâmicos</td>
  </tr>
  <tr>
    <td>Dados Estáticos</td>
  </tr>
  <tr>
    <td>Texto</td>
  </tr>
  <tr>
    <td>Reservado</td>
  </tr>
</table>

