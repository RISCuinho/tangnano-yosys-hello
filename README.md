# tangnano-yosys-hello

![visitors](https://visitor-badge.laobi.icu/badge?page_id=RISCuinho.tangnano-yosys-hello)
[![License: CC BY-SA 4.0](https://img.shields.io/badge/License-CC_BY--SA_4.0-blue.svg)](https://creativecommons.org/licenses/by-sa/4.0/)
![Language: Portuguese](https://img.shields.io/badge/Language-Portuguese-brightgreen.svg)
![Verilog](https://img.shields.io/badge/Verilog-HDL-blue)
![FPGA](https://img.shields.io/badge/FPGA-Gowin-green)
![Toolchain](https://img.shields.io/badge/Toolchain-Opensource-orange)
![Status](https://img.shields.io/badge/Status-Funcional-brightgreen)

## Sipeed Tang Nano - Toolchain Open Source para LED Blink

Este projeto demonstra o uso de uma toolchain completamente open source para desenvolvimento FPGA na placa **Sipeed Tang Nano**, utilizando o chip Gowin GW1N-1. O projeto implementa um simples exemplo de "LED Blink" que pisca os LEDs RGB da placa em diferentes frequências, servindo como ponto de partida para desenvolvedores que desejam trabalhar com FPGAs usando ferramentas de código aberto.

## Propósito do Projeto

O objetivo principal deste projeto é:

- **Demonstrar** a viabilidade de usar ferramentas open source (Yosys, nextpnr-gowin, Apicula) para desenvolvimento FPGA
- **Fornecer** um exemplo mínimo e funcional para iniciantes em FPGA
- **Documentar** o fluxo completo de síntese, placement, routing e programação
- **Estabelecer** uma base para projetos mais complexos usando a mesma toolchain
- **Promover** o uso de ferramentas livres no ecossistema FPGA

## Estrutura do Projeto

```
tangnano-yosys-hello/
├── blinky.v          # Módulo Verilog principal (LED Blink)
├── tangnano.cst      # Arquivo de constraints (pin assignment)
├── Makefile          # Automatização do build e programação
├── top.json          # Netlist gerado pelo Yosys (gerado)
├── top.pack          # Placement & routing (gerado)
├── top.fs            # Bitstream final para programação (gerado)
└── README.md         # Este arquivo
```

## Descrição do Hardware

### Placa: Sipeed Tang Nano


- **FPGA**: Gowin GW1N-1 LV1QN48C6/I5
- **LUTs**: 1152
- **FFs**: 864
- **SRAM**: 43.2 Kb
- **Clock**: 27MHz interno
- **LEDs**: RGB onboard
- **Interface**: USB para programação

### Funcionalidade do Código



O módulo `blinky.v` implementa:
- Contador de 26 bits incrementado a cada borda de subida do clock de 27MHz
- Divisão de clock para criar frequências visíveis de pisca
- Conexão dos 3 bits mais significativos do contador aos LEDs RGB
- Efeito de cores misturadas devido às diferentes frequências

## Requisitos

### Software


- Docker
- Make
- openFPGALoader (para programação)

### Hardware


- Sipeed Tang Nano
- Cabo USB

## Fluxo de Desenvolvimento

O projeto utiliza uma toolchain 100% open source:

1. **Yosys** - Síntese de Verilog para netlist JSON
2. **nextpnr-gowin** - Placement e routing
3. **Apicula** - Geração do bitstream final

### Build

O processo de build é automatizado via Docker:

```bash
make
```

Este comando executa:
- Síntese com Yosys: `yosys -p "synth_gowin -json top.json -top top" blinky.v`
- Placement & routing: `nextpnr-gowin --json top.json --write top.pack --device GW1N-LV1QN48C6/I5 --cst tangnano.cst`
- Geração do bitstream: `gowin_pack -d GW1N-1 -o top.fs top.pack`

### Programação

Para programar a placa Tang Nano:

```bash
make prog
```

Ou diretamente:

```bash
openFPGALoader -b tangnano top.fs
```

### Limpeza

Para remover os arquivos gerados:

```bash
make clean
```

### Explicações Técnicas

#### Frequências dos LEDs



Com um clock de 27MHz e um contador de 26 bits:
- **LED_R** (bit 25): pisca a ~0.4 Hz (período de ~2.5 segundos)
- **LED_G** (bit 24): pisca a ~0.8 Hz (período de ~1.25 segundos)  
- **LED_B** (bit 23): pisca a ~1.6 Hz (período de ~0.6 segundos)

#### Arquivo de Constraints (tangnano.cst)



Define o mapeamento dos pinos físicos da placa:
- `SYS_CLK` no pino 35 (clock de 27MHz)
- `LED_R`, `LED_G`, `LED_B` nos pinos 16, 17, 18 respectivamente
- Configurações de I/O para LVCMOS33

### Próximos Passos

Este projeto serve como base para:
- Adicionar mais periféricos (botões, LCD, PSRAM)
- Implementar designs mais complexos
- Explorar otimizações de timing e área
- Integrar com softcores RISC-V

### Licença

Este projeto está licenciado sob Creative Commons Attribution-ShareAlike 4.0 International License.

### Referências

- [Sipeed Tang Nano Wiki](https://wiki.sipeed.com/hardware/en/tang/tang-nano.html)
- [Yosys](http://www.clifford.at/yosys/)
- [nextpnr](https://github.com/YosysHQ/nextpnr)
- [Apicula](https://github.com/YosysHQ/apicula)
- [openFPGALoader](https://github.com/trabucayre/openFPGALoader)
