# Sistema de Controle de Acesso com Raspberry Pi Pico

Projeto desenvolvido para simular um sistema de controle de acesso utilizando a Raspberry Pi Pico com FreeRTOS. O sistema monitora a entrada e sa�da de pessoas em um ambiente com capacidade limitada, fornecendo feedback visual e sonoro em tempo real sobre o status de ocupa��o.

## Objetivo

Desenvolver um sistema embarcado de controle de acesso que gerencia a capacidade m�xima de um ambiente (10 pessoas), utilizando sem�foros de contagem do FreeRTOS para sincroniza��o entre tarefas. O projeto oferece interface intuitiva com display OLED, indicadores LED RGB, matriz de LEDs animada e feedback sonoro.

## Funcionalidades

### Estado Vazio
- **Condi��o**: 0 pessoas no ambiente
- Display OLED mostra "PODE ENTRAR" 
- LED RGB em **azul**
- Matriz de LEDs com seta azul piscante
- Buzzer inativo

### Estado Normal  
- **Condi��o**: 1 a 8 pessoas no ambiente
- Display OLED exibe contador atual e "PODE ENTRAR"
- LED RGB em **verde**
- Matriz de LEDs com seta verde piscante  
- Buzzer inativo

### Estado de Aten��o
- **Condi��o**: 9 pessoas (apenas 1 vaga restante)
- Display OLED mostra "APENAS 1 VAGA"
- LED RGB em **amarelo** 
- Matriz de LEDs com seta amarela piscante
- Buzzer inativo

### Estado de Alerta
- **Condi��o**: 10 pessoas (capacidade m�xima)
- Display OLED exibe "N�MERO M�XIMO"
- LED RGB em **vermelho**
- Matriz de LEDs com seta vermelha piscante
- Buzzer toca alerta quando tentativa de entrada � bloqueada

### Fun��o Reset
- **Acionamento**: Bot�o do joystick
- Zera contador de pessoas instantaneamente
- Exibe mensagem "SISTEMA RESETADO" no display
- Buzzer toca sequ�ncia de confirma��o (2 bips grave-agudo)

## Componentes e GPIOs Utilizados

| Componente | GPIO | Fun��o |
|------------|------|--------|
| Bot�o A | GP5 | Entrada de pessoas |
| Bot�o B (BOOTSEL) | GP6 | Sa�da de pessoas |
| Bot�o Joystick | GP22 | Reset do sistema (via interrup��o) |
| Display OLED SSD1306 | GP14/GP15 | Exibe status e contador |
| LED RGB (PWM) | GP11-13 | Indica��o de estado por cores |
| Buzzer (PWM) | GP21 | Alertas sonoros |
| Matriz de LEDs 5x5 (PIO) | GP7 | Anima��es de setas coloridas |

## Multitarefa com FreeRTOS

O sistema utiliza FreeRTOS para execu��o concorrente e sincronizada:

### Tarefas Implementadas
- **vEntradaTask**: Monitora bot�o A para entrada de pessoas
- **vSaidaTask**: Monitora bot�o B para sa�da de pessoas  
- **vResetTask**: Processa reset via interrup��o do joystick
- **vDisplayTask**: Atualiza informa��es no display OLED
- **vLeds_RGBTask**: Controla cores dos LEDs baseado no estado
- **vBuzzerTask**: Gerencia alertas sonoros
- **vMatriz_LedsTask**: Controla anima��es da matriz 5x5

### Sincroniza��o
- **Sem�foro de Contagem**: Controla n�mero de pessoas (m�x. 10)
- **Sem�foro Bin�rio**: Sinaliza reset entre interrup��o e tarefa
- **Mutexes**: Protegem acesso ao display e vari�veis compartilhadas

## T�cnicas Implementadas

### Hardware
- **PWM**: Controle de brilho dos LEDs RGB e frequ�ncia do buzzer
- **PIO**: Controle customizado da matriz de LEDs WS2812B
- **Interrup��es GPIO**: Detec��o do bot�o de reset com debounce
- **I2C**: Comunica��o com display OLED

### Software  
- **Debounce por Software**: Filtro de 250ms para bot�es
- **Debounce por Hardware**: Filtro de 300ms para interrup��o
- **Sem�foros de Contagem**: Implementa��o de fila de pessoas
- **Prote��o de Concorr�ncia**: Mutexes para recursos compartilhados
- **M�quina de Estados**: Controle visual baseado em n�mero de pessoas

## Estados do Sistema

```
VAZIO (0) ? NORMAL (1-8) ? ATEN��O (9) ? ALERTA (10)
    ?           ?              ?           ?
   Azul       Verde        Amarelo     Vermelho
```

## Estrutura do C�digo

```
??? Configura��es de Hardware
??? Vari�veis Globais e Sem�foros  
??? Handlers de Interrup��o
??? Fun��es de Debounce
??? Controle PWM e PIO
??? Anima��es da Matriz de LEDs
??? Controle do Buzzer
??? 7 Tarefas FreeRTOS
??? Fun��o Principal (main)
```

## Como Usar

1. **Entrada**: Pressione o Bot�o A para adicionar uma pessoa
2. **Sa�da**: Pressione o Bot�o B para remover uma pessoa  
3. **Reset**: Pressione o bot�o do joystick para zerar o sistema
4. **Monitoramento**: Observe o display OLED e LEDs para status atual

## Autor

**Mateus Moreira da Silva**  
Data: 26-05-2025

GitHub: