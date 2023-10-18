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
1. Coloque os argumentos (parâmetros) nos registradores
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

Se os 4 registradores para argumentos não forem suficientes, pode-se usar a pilha (heap). O `$sp` (stack pointer) aponta para o topo da pilha. O `$sp` é decrementado quando um valor é empilhado e incrementado quando um valor é desempilhado.


O ajuste de armazenamento é justamente o acesso à pilha
### Pilha
#### Memória
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
    <td></td>
  </tr>
  <tr>
    <td>Texto</td>
    <td>Código</td>
  </tr>
  <tr>
    <td>Reservado</td>
    <td></td>
  </tr>
</table>

#### Inserção na Pilha
Exemplo: Salvar `$s0` e `$s1` na pilha
<table>
  <tr><th colspan="3">Zoom na pilha</th></tr>
  <tr>
    <td>101</td>
    <td></td>
    <td>inicialmente <code>$sp</code> aqui</td>
  </tr>
  <tr>
    <td>100</td>
    <td rowspan="4">$s1</td>
  </tr>
  <tr>
    <td>99</td>
  </tr>
  <tr>
    <td>98</td>
  </tr>
  <tr>
    <td>97</td>
    <td>depois <code>$sp</code> aqui</td>
  </tr>
  <tr>
    <td>96</td>
    <td rowspan="4">$s0</td>
  </tr>
  <tr>
    <td>95</td>
  </tr>
  <tr>
    <td>94</td>
  </tr>
  <tr>
    <td>93</td>
    <td>por último <code>$sp</code> aqui</td>
  </tr>
</table>

```asm
# abrir espaço
addi $sp, $sp, -8 # $sp = $sp - 8
# salva na pilha
sw $s0, 0($sp) # salva $s0 na pilha
sw $s1, 4($sp) # salva $s1 na pilha
```
#### Remoção na Pilha
Exemplo: Recuperar `$s0` e `$s1` da pilha
```asm
# restaura os valores
lw $s0, 0($sp) # recupera $s0 da pilha
lw $s1, 4($sp) # recupera $s1 da pilha
# restaura o espaço na pilha
addi $sp, $sp, 8 # $sp = $sp + 8
```

### Desviar para o procedimento
```asm
jal procedimento # jump and link
```
O `jal` salva o endereço da próxima instruçã no registrador `$ra (returning adress)` (return address) e desvia para o rótulo "procedimento"

### Retornar ao chamador
```asm
jr $ra # jump to returning
```
## Exemplo: função média
```c
int media(int a, int b){
    // a sendo o $a0 e b sendo o $a1
    return (a+b)/2;
}
int main(){
  int a=3; // $s0
  int b=2; // $s1
  printf("%d\n", media(a,b));
  return 0;
}
```
```asm
main:
  li $s0, 3 # a = 3
  li $s1, 2 # b = 2
  # carregar os argumentos
  move $a0, $s0 # $a0 = a
  move $a1, $s1 # $a1 = b
  # chamar o procedimento
  jal media
  # Imprime o resultado
  move $a0, $v0 # $a0 = $v0
  li $v0, 1 # codigo para impressao de int
  syscall
  # imprimir o \n pode ser por syscall usando o código ascii 10 ou pela seção .data
  li $v0, 11 # codigo para imprimir \n
  li $a0, 10 # codigo ascii para \n
  syscall
  li $v0, 10 # codigo para sair do programa
  syscall
media:
  add $v0, $a0, $a1 # $v0 = a + b
  srl $v0, $v0, 1 # $v0 = $v0 / 2
  jr $ra # retorna ao chamador
```
## Exemplo: função média que chama função soma
```c
int soma(int a, int b){
  return a+b;
}
int media(int a, int b){
    // a sendo o $a0 e b sendo o $a1
    return soma(a,b)/2;
}
int main(){
  int a=3; // $s0
  int b=2; // $s1
  printf("%d\n", media(a,b));
  return 0;
}
```
A main chama média, e média chama soma. Nesse, caso é preciso salvar o `$ra` da instrução seguinte à média. Quando a main chama a média, o `$ra` passa a ser o endereço da instrução seguinte à chamada da média. Quando a média chama a soma, o `$ra` passa a ser o endereço da instrução seguinte à chamada da soma. Quando a soma retorna, o `$ra` continua sendo o endereço da instrução seguinte à chamada da soma (viraria um loop infinito). 

Por esse motivo, é necessário salvar o endereço da instrução seguinte à chamada de média antes de chamar a soma. Isso é feito empilhando o `$ra` antes de chamar a soma e desempilhando depois de retornar da soma.
```asm
main:
  li $s0, 3 # a = 3
  li $s1, 2 # b = 2
  # carregar os argumentos
  move $a0, $s0 # $a0 = a
  move $a1, $s1 # $a1 = b
  # chamar o procedimento
  jal media # chama a função media
  # Imprime o resultado
  move $a0, $v0 # $a0 = $v0
  li $v0, 1 # codigo para impressao de int
  syscall
  # imprimir o \n pode ser por syscall usando o código ascii 10 ou pela seção .data
  li $v0, 11 # codigo para imprimir \n
  li $a0, 10 # codigo ascii para \n
  syscall
  li $v0, 10 # codigo para sair do programa
  syscall
media:
  addi $sp, $sp, -4 # abre espaço na pilha
  sw $ra, 0($sp) # salva $ra na pilha
  jal soma # chama a função soma
  srl $v0, $v0, 1 # $v0 = $v0 / 2
  lw $ra, 0($sp) # recupera $ra da pilha
  addi $sp, $sp, 4 # fecha espaço na pilha
  jr $ra # retorna ao chamador
soma:
  add $v0, $a0, $a1 # $v0 = a + b
  jr $ra # retorna ao chamador
```
# Formato tipo J
| op | endereço |
| :--: | :--: |  
| 6 bits | 26 bits |

É o formato das intruções `j`, `jal` e `jr`

# Manipulação de caracteres
## Tabela ASCII
```c
printf("%d\n", '2' - '0') // retorna 2
```
`'a' - 'A' = 32`
## Instruções de acesso à memória
```asm
# load byte
lb $t0, 0($s0) # carrega 1 byte
# load byte unsigned
lbu $t0, 0($s0) # carrega 1 byte
# store byte
sb $t0, 0($s0) # escreve 1 byte
```
## System calls
* 11 - imprime caractere
* 12 - lê caractere

$\color{red}\text{Encerrando linguagem de montagem}$