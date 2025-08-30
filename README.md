# 📟 Monitor Ambiente com Raspberry Pi Pico W

Este projeto implementa um **sistema embarcado** utilizando o **Raspberry Pi Pico W** que realiza:

- Medição de **distância** com o sensor **VL53L0X** (ToF).  
- Medição de **temperatura e umidade** com o sensor **AHT10** (I²C).  
- Registro de todas as leituras em um **cartão SD** via **SPI**.  
- Exibição em tempo real no **OLED SSD1306**.  
- Indicação visual com **LEDs coloridos** para diferentes estados de proximidade.  
- Consulta ao **histórico de medições** diretamente no display, usando **botões físicos**.  

---

## 🛠️ Hardware Utilizado

- Raspberry Pi Pico W  
- Sensor de distância **VL53L0X** (I²C)  
- Sensor de temperatura/umidade **AHT10** (I²C)  
- Display OLED **SSD1306** (I²C)  
- Cartão microSD + módulo SPI  
- LEDs RGB (verde, vermelho, azul)  
- 2 botões físicos para interação (histórico e parar histórico)  

---

## 📐 Ligações

### I²C (VL53L0X, AHT10 e SSD1306)
- **SDA:** GPIO 0  
- **SCL:** GPIO 1  

### SPI (Cartão SD)
- **MISO:** GPIO 16  
- **CS:** GPIO 17  
- **SCK:** GPIO 18  
- **MOSI:** GPIO 19  

### Botões
- **Botão Histórico:** GPIO 5  
- **Botão Stop:** GPIO 6  

### LEDs
- Conectados conforme definido em `leds.h`  
  - **Verde**
  - **Vermelho**
  - **Azul**

---

## 💡 Funcionamento

1. O sistema inicializa os periféricos (I²C, SPI, OLED, LEDs, botões).  
2. O **VL53L0X** mede a distância continuamente, com filtro de média móvel.  
3. O **AHT10** fornece temperatura (°C) e umidade relativa (%).  
4. O **OLED SSD1306** exibe:  
   - Estado do objeto (longe, médio, alerta, fora de alcance)  
   - Distância filtrada (mm)  
   - Temperatura e umidade ambiente  
5. Os dados são registrados no **cartão SD** em `dados.txt` no formato:  

[00:12] Dist: 250 mm | Estado: MEDIO | Temp: 26.5C | Umid: 55.2%

6. O **LED RGB** muda conforme a condição:  
- 🔴 Vermelho: objeto muito próximo (≤ 10 cm)  
- 🟢 Verde: objeto a média distância (até ~30 cm)  
- 🔵 Azul: objeto longe  
- ⚫ Desligado: fora de alcance  

7. Ao pressionar o **botão histórico (GPIO 5)**, o conteúdo do `dados.txt` é exibido no OLED com **scroll automático**.  
- O botão **stop (GPIO 6)** interrompe a leitura.  

---

## 📂 Estrutura do Projeto

/src
├── main.c
├── vl53l0x.c / vl53l0x.h
├── aht10.c / aht10.h
├── leds.c / leds.h
├── inc/
│ ├── ssd1306.h
│ ├── ssd1306_fonts.h
└── ff.h (FatFs)

---

## 🚀 Como Compilar e Rodar

1. Instale o **SDK do Raspberry Pi Pico** e configure o CMake.  
2. Clone este repositório e compile:  

   ```bash
   mkdir build
   cd build
   cmake ..
   make

Copie o .uf2 gerado para o Pico W em modo BOOTSEL.

Abra o terminal serial para acompanhar os logs (distância, estado, temperatura, umidade).

📝 Melhorias Futuras

Exportar dados em formato CSV (;) para facilitar análise no Excel.

Implementar debounce via IRQ nos botões.

Criar gráficos em tempo real no OLED.

Suporte a Wi-Fi (MQTT/HTTP) para enviar dados à nuvem.