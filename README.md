### **Tutorial: Usando a Biblioteca Não Oficial Heltec ESP32 LoRa V3**

Este tutorial explica detalhadamente como utilizar a biblioteca não oficial **Heltec ESP32 LoRa V3**, projetada para simplificar o uso das placas Heltec ESP32 LoRa V3. Combinando um transceptor LoRa SX1262, um ESP32-S3 com conectividade Wi-Fi/Bluetooth e, em alguns casos, um display OLED, essas placas são perfeitas para projetos de IoT e comunicação de longo alcance.

---

## **1. Introdução à Biblioteca**
A biblioteca não oficial substitui a complexidade das ferramentas oficiais da Heltec, oferecendo:
- **Integração com a RadioLib:** Simplifica a comunicação LoRa/LoRaWAN.
- **Controle do display OLED:** Inclui suporte à biblioteca ThingPulse.
- **Gerenciamento de energia:** Permite medições de tensão e gerenciamento de bateria.
- **Suporte ao botão PRG:** Usado como botão multifuncional, incluindo controle de energia.

> **Requisitos:**
> - Placa Heltec ESP32 LoRa V3 ou variantes (Stick V3 ou Stick Lite V3).
> - Arduino IDE ou plataforma equivalente configurada para ESP32.
> - Biblioteca Heltec não oficial (disponível no [GitHub](https://github.com/ropg/heltec_esp32_lora_v3)).

---

## **2. Instalação**
### **2.1. Configuração da IDE Arduino**
1. **Adicionar o Gerenciador de Placas ESP32:**
   - Vá para **Arquivo > Preferências**.
   - Adicione o URL:  
     `https://espressif.github.io/arduino-esp32/package_esp32_index.json`  
     no campo **URLs Adicionais para Gerenciadores de Placas**.

2. **Instalar as Placas ESP32:**
   - Vá para **Ferramentas > Placa > Gerenciador de Placas**.
   - Busque por "ESP32" e instale o pacote fornecido por **Espressif Systems**.

3. **Selecionar a Placa Correta:**
   - Após instalar, escolha a placa **Heltec WiFi LoRa 32(V3) / Wireless shell(V3)** em **Ferramentas > Placa**.

---

### **2.2. Instalar a Biblioteca Heltec Não Oficial**
1. No Arduino IDE, vá para **Ferramentas > Gerenciador de Bibliotecas**.
2. Busque por **heltec_esp32** e instale a biblioteca "The Unofficial Heltec ESP32 LoRa V3".

> A biblioteca automaticamente instala dependências, incluindo:
> - **RadioLib:** Para comunicação LoRa.
> - **ThingPulse OLED:** Para controle do display.

---

## **3. Principais Funcionalidades**
A seguir, exploraremos os recursos oferecidos pela biblioteca.

---

### **3.1. Comunicação LoRa**
#### **Configuração Básica**
O exemplo abaixo configura o transceptor LoRa para enviar e receber mensagens:

```cpp
#include <heltec_unofficial.h>

void setup() {
  heltec_setup(); // Inicializa o rádio, display e botão
  Serial.println("Iniciando comunicação LoRa...");

  if (radio.begin() == RADIOLIB_ERR_NONE) {
    Serial.println("Rádio configurado com sucesso!");
    radio.setFrequency(866.3);           // Configurar frequência em MHz
    radio.setSpreadingFactor(9);         // Configurar Spreading Factor
    radio.setOutputPower(10);            // Configurar potência em dBm
  } else {
    Serial.println("Erro ao inicializar o rádio.");
  }
}

void loop() {
  // Transmitir mensagem
  String message = "Hello LoRa!";
  if (radio.transmit(message) == RADIOLIB_ERR_NONE) {
    Serial.println("Mensagem enviada com sucesso!");
  } else {
    Serial.println("Erro ao enviar mensagem.");
  }

  delay(2000); // Pausa antes da próxima transmissão
}
```

---

#### **Receber Mensagens**
```cpp
void loop() {
  String receivedMessage;
  if (radio.receive(receivedMessage) == RADIOLIB_ERR_NONE) {
    Serial.println("Mensagem recebida:");
    Serial.println(receivedMessage);

    Serial.printf("RSSI: %.2f dBm\n", radio.getRSSI());
    Serial.printf("SNR: %.2f dB\n", radio.getSNR());
  }
}
```

---

### **3.2. Display OLED**
#### **Configuração**
A biblioteca usa a ThingPulse para controle do display OLED. Após incluir `heltec_unofficial.h`, o display está disponível via `display`.

#### **Exemplo: Exibir Texto**
```cpp
#include <heltec_unofficial.h>

void setup() {
  heltec_setup();
  display.clear();
  display.drawString(0, 0, "Hello, OLED!");
  display.display();
}

void loop() {}
```

---

### **3.3. Gerenciamento de Energia**
#### **Medir Tensão da Bateria**
```cpp
void loop() {
  float vbat = heltec_vbat();
  Serial.printf("Tensão da bateria: %.2f V\n", vbat);
  delay(5000);
}
```

#### **Modo Deep Sleep**
Economize energia colocando a placa em deep sleep:
```cpp
void setup() {
  heltec_deep_sleep(10); // Dormir por 10 segundos
}
```

---

### **3.4. Botão PRG**
#### **Detectar Cliques**
```cpp
void loop() {
  if (button.isSingleClick()) {
    Serial.println("Botão pressionado (clique único).");
  }
  if (button.isDoubleClick()) {
    Serial.println("Botão pressionado (clique duplo).");
  }
}
```

#### **Usar como Botão de Liga/Desliga**
```cpp
#define HELTEC_POWER_BUTTON
#include <heltec_unofficial.h>

void setup() {
  heltec_setup();
}

void loop() {
  heltec_loop(); // Necessário para o botão funcionar
}
```

---

## **4. Projetos Práticos**
Aqui estão alguns projetos simples que você pode criar com essa biblioteca:

### **4.1. Comunicação Ponto-a-Ponto**
Configure duas placas Heltec para trocar mensagens LoRa. Use o exemplo de transmissão e recepção acima, ajustando as frequências.

---

### **4.2. Monitor de Bateria**
Exiba a tensão da bateria no display OLED:
```cpp
void loop() {
  float vbat = heltec_vbat();
  display.clear();
  display.printf("Bateria: %.2f V", vbat);
  display.display();
  delay(5000);
}
```

---

### **4.3. Rede de Sensores**
Combine LoRa e deep sleep para criar sensores de longa duração que reportam dados periodicamente.

---

## **5. Dicas e Truques**
- **Problemas com Frequências:** Verifique as regulamentações locais para o uso de LoRa.
- **Depuração:** Use o serial monitor para depurar mensagens e status do rádio.
- **Biblioteca RadioLib:** Para projetos avançados, explore diretamente a documentação da RadioLib.

---

## **6. Conclusão**
A biblioteca Heltec ESP32 LoRa V3 não oficial simplifica o uso das placas Heltec, fornecendo ferramentas poderosas para comunicação LoRa, gerenciamento de energia e integração com displays. Seja para monitoramento remoto, redes IoT ou protótipos, essa biblioteca é uma excelente escolha para desenvolvedores.

> **Explore Mais:**
> - [Documentação da Biblioteca](https://github.com/ropg/heltec_esp32_lora_v3)
> - [RadioLib](https://jgromes.github.io/RadioLib/)

Se precisar de ajuda, é só perguntar! 🚀
