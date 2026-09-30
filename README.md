# 🚦 Semáforo-Stm32-Núcleo-L031K6

Simulação de um semáforo com botão de travessia para pedestres, feita com a placa STM32 Nucleo L031K6 no Wokwi.

---

## 📋 Sobre o projeto

A ideia foi simular um cruzamento com semáforo para carros e semáforo para pedestres. Em repouso, o sinal dos carros fica verde e o dos pedestres fica vermelho. Quando o pedestre aperta o botão, o semáforo dos carros passa pela sequência de aviso (vermelho e amarelo piscando) e fecha, liberando a travessia. Depois do tempo de travessia, tudo volta ao estado inicial.

O projeto foi feito como parte do meu portfólio prático de sistemas embarcados, usando o Wokwi como ambiente de simulação.

▶️ [Abrir a simulação no Wokwi](https://wokwi.com/projects/476164077025953793)

---

## 🛠 Ferramentas utilizadas

- Wokwi (simulador)
- STM32 Nucleo L031K6
- Protoboard
- 2 LEDs vermelhos
- 1 LED amarelo
- 2 LEDs verdes
- 5 resistores de 150 Ω
- 1 botão (push button)
- Linguagem C++ (API do Arduino)

---

## 🏗 O que foi montado

O circuito tem três blocos principais:

- Semáforo dos carros: três LEDs (verde, amarelo e vermelho) ligados aos pinos A0, A1 e A2, cada um com um resistor de 150 Ω em série e o outro lado no GND.
- Semáforo dos pedestres: dois LEDs (verde e vermelho) ligados aos pinos A3 e A4, também com resistor de 150 Ω em série.
- Botão de travessia: ligado entre o pino A5 e o GND. O pino usa o resistor de pull-up interno da placa, então não precisa de resistor externo.

### Pinagem

| Componente | Pino da STM32 Nucleo L031K6 |
|---|---|
| LED verde (carros) | A0 |
| LED amarelo (carros) | A1 |
| LED vermelho (carros) | A2 |
| LED verde (pedestres) | A3 |
| LED vermelho (pedestres) | A4 |
| Botão de travessia | A5 |

---

## 🔧 Como funciona

1. No estado inicial, o LED verde dos carros e o LED vermelho dos pedestres ficam acesos.
2. Quando o botão é pressionado, o código espera 50 ms e confirma se ele continua pressionado (debounce simples).
3. O verde dos carros continua aceso por 5 segundos e depois apaga.
4. O vermelho dos carros pisca 3 vezes e, em seguida, o amarelo pisca 4 vezes.
5. O vermelho dos carros acende, o verde dos pedestres acende e o vermelho dos pedestres apaga.
6. Os pedestres têm 5 segundos para atravessar.
7. Os LEDs de travessia apagam e o ciclo volta ao estado inicial.

---

## 💻 Código

```cpp
// Semáforo-Stm32-Núcleo-L031K6

// Define os pinos dos LEDs do semáforo dos carros
#define LED_S_VERDE A0 
#define LED_S_AMARELO A1  
#define LED_S_VERMELHO A2 

// Define os pinos dos LEDs do semáforo dos pedestres
#define LED_P_VERDE A3 
#define LED_P_VERMELHO A4 

// Define o pino do botão para solicitar a travessia
#define BOTAO A5

void setup() {
  // LEDs do semáforo como "saída"
  pinMode(LED_S_VERDE, OUTPUT);
  pinMode(LED_S_AMARELO, OUTPUT);
  pinMode(LED_S_VERMELHO, OUTPUT);

  // LEDs do pedestre como "saída"
  pinMode(LED_P_VERDE, OUTPUT);
  pinMode(LED_P_VERMELHO, OUTPUT);

  // Configura o botão como entrada utilizando o resistor de pull-up interno
  pinMode(BOTAO, INPUT_PULLUP);
}

void loop() {
  // Estado inicial, ambos ligados
  digitalWrite(LED_S_VERDE, HIGH);
  digitalWrite(LED_P_VERMELHO, HIGH);

  // Espera o botão ser pressionado
  if (digitalRead(BOTAO) == LOW) 
  {
    // Debounce simples
    delay(50);
    
    // Confirma se o botão continua pressionado
    if (digitalRead(BOTAO) == LOW)
    {
      // Mantém o verde do semáforo durante 5 segundos
      delay(5000);

      // Apaga o verde do semáforo
      digitalWrite(LED_S_VERDE, LOW);

      // Vermelho do semáforo piscando três "i < 3;" vezes 
      for (int i = 0; i < 3; i++)
      {
        // Ligado
        digitalWrite(LED_S_VERMELHO, HIGH);
        delay(500);
        // Desligado
        digitalWrite(LED_S_VERMELHO, LOW);
        delay(500);
      }

      for (int i = 0; i < 4; i++)
      {
        // Ligado
        digitalWrite(LED_S_AMARELO, HIGH);
        delay(500);
        // Desligado
        digitalWrite(LED_S_AMARELO, LOW);
        delay(500);
      }
      // Semáfro vermelho ligado
      digitalWrite(LED_S_VERMELHO, HIGH);

      // Pedestre verde ligado
      digitalWrite(LED_P_VERDE, HIGH);

      // Pedestre vermelho desligado
      digitalWrite(LED_P_VERMELHO, LOW);

      // Tempo para o pedestre atravessar
      delay(5000);

      // LED verde do pedestre desligado
      digitalWrite(LED_P_VERDE, LOW);

      // LED vermelho do semáforo desligado
      digitalWrite(LED_S_VERMELHO, LOW);
    }
  }
}
```

---

## 📸 Evidências do funcionamento

### Circuito montado
A imagem mostra a placa STM32 Nucleo L031K6, a protoboard com os cinco LEDs, os resistores e o botão de travessia.

![Circuito no Wokwi](imagens/circuito_wokwi.jpg)

---

## 📁 Arquivo do diagrama

O arquivo diagram.json com todas as peças e conexões está disponível no repositório e pode ser importado direto no Wokwi.

[📥 Download — diagram.json](diagram.json)

---

## 💡 O que aprendi com esse projeto

O principal foi montar uma sequência de estados usando apenas `digitalWrite`, `delay` e laços `for`, controlando cinco LEDs em ordem para simular um semáforo real.

Também aprendi a usar o resistor de pull-up interno (`INPUT_PULLUP`) no botão, o que dispensa resistor externo. Com ele, o botão é lido como `LOW` quando pressionado. Junto disso, apliquei um debounce simples para evitar leituras falsas.

---

## ⚠️ Sobre o projeto

Essa simulação é uma base para estudo. Como o código usa `delay()`, a placa não lê o botão enquanto a sequência está rodando. Uma evolução natural seria trocar os `delay()` por `millis()` e organizar as etapas como uma máquina de estados.

---

## 🚀 Próximos projetos

Outros projetos de sistemas embarcados serão postados em breve, com níveis de complexidade maiores.

---

## 👤 Autor: Bruno Zucker
