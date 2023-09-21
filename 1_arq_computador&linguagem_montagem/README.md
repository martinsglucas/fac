# Arquitetura de um computador
Compreende um projeto estrutural de um computador, ou seja, componentes lógicos que determinam seu funcionamento  
Um sistema computacional atualmente compreende três camadas:
* **Camada externa**: Software de aplicação
* **Camada intermediária**: Software de sistemas
* **Camada interna**: Hardware
<div align="center">
<img src="fases_codigo.png" alt="Aplicação > Linguagem alto nível > Linguagem de montagem > Linguagem de máquina" width="400" height="400"></div>

A arquiterura de um computador, definida nas suas instruções, é chamada Arquiterura do Conjunto de Instruções (ISA). O ISA pode ser classificado como RISC ou CISC
RISC (Reduced Instruction Set Computer) possui poucas instruções, simples e no mesmo padrão
CISC (Complex Instructio Set Computer) possui centenas de instruções em seu conjunto, capazes de executar uma grande diversidade de operações  

 | | RISC | CISC
 :--: | :--: | :--:
 Instruções | menor quantidade; mais simples e padronizado | maior quantidade; mais complexas
 Projeto | centrado no software; programar mais | centrado no hardware
 Execução | executa direto no hardware com uso de controladora | a instrução é traduzida por um microprograma que pode consumir vários ciclos do processador

# Linguagem de montagem (assembly)
Arquitetura MIPS 32 bits  
Simulador SPIM
```bash
spim -f codigo.spim
```
Para instalar o SPIM no linux, digite no terminal:
```bash
apt install spim
```
## Operações Aritméticas
```asm
add a,b,c # a = b + c
```
Todas as instruções aritméticas seguem esse formato  
Quem são as variáveis? Registradores (tem 32 bits = 4 bytes)  
Registradores são unidades de memória de acesso imediato que ficam dentro do processador  
Um processador MIPS de 32 bits possui 32 registradores em sua unidade principal, que opera com inteiros  
Os operadores disponíveis para uso são:  
Notação | ID | Info
:--: | :--: | :--:
$t0 a $t7 | 8-15 | 8 regs temporário
$s0 a $s7 | 16-23 | 8 regs salvos
$t8 e $t9 | 24-25 | 2 regs temporário  

**Obs:** Em situações de multiprocessos há a convenção de que os registradores 't' podem ser sobrescritos e não devem armazenar informação importante,
enquanto registradores 's' não devem ser sobrescritos
OS 14 registradores restantes são de uso reservado. Veremos alguns adiante  

O código `f = (g+h) - (i+j)` ficaria da seguinte forma em assembly:
```asm
# f = $s0, g = $s1, h = $s2, i = $s3, j = $s4
add $t0, $s1, $s2 # t0 = g + h
add $t1, $s3, $s4 # t1 = i + j
sub $s0, $t0, $t1 # f = t0 - t1
```
