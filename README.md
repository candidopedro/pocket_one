# Pocket One

Editor web **não-oficial** para o pedal multiefeitos **Sonicake Pocket Master (QME-10)**, construído inteiramente via engenharia reversa do protocolo SysEx/MIDI usado pelo aplicativo oficial.

Este projeto é baseado no **Pocket Edit** e segue evoluindo a partir dele sob o novo nome **Pocket One**.

Roda 100% no navegador — sem instalação, sem backend, sem build — usando a **Web MIDI API** para falar diretamente com o pedal por USB ou Bluetooth LE.

> ⚠️ **Aviso:** este é um projeto independente, feito por engenharia reversa, e **não tem qualquer vínculo com a Sonicake**. Use por sua conta e risco — comandos SysEx malformados podem deixar o pedal em um estado inesperado.

## Sobre o projeto

O Pocket Master é vendido com um software oficial (Soniclink / Sonicake Manager) para edição de presets. Este projeto nasceu da engenharia reversa do protocolo SysEx usado por esse app para criar uma alternativa **web, aberta e offline-first**, além de documentar o protocolo descoberto para quem quiser explorar ou estender o hardware.

## Funcionalidades

- **Conexão direta com o pedal** via Web MIDI (USB ou Bluetooth LE), com indicador de status de conexão.
- **Lista de presets** de fábrica e de usuário, com busca por nome.
- **Editor da cadeia de sinal** com os módulos do pedal: `NR` (noise reduction), `FX1`, `DRV` (drive), `AMP`, `IR` (cabinet sim), `EQ`, `FX2`, `DLY` (delay) e `RVB` (reverb), cada um com seus parâmetros e biblioteca de efeitos.
- **Controles de volume** global e por preset.
- **Salvar preset** em um slot do usuário, com renomeação.
- **Exportar / Importar presets** como arquivo `.json`.
- **Painel de configurações avançadas**, com:
  - Log de comandos MIDI em tempo real (enviados/recebidos).
  - Envio manual de comandos SysEx para depuração.
  - Sincronização de nomes de presets, módulos, IRs de usuário, amps clonados e configurações globais.

## Como funciona

O projeto é HTML, CSS e JavaScript puro (sem frameworks, sem etapa de build). Toda a comunicação com o pedal acontece pela `navigator.requestMIDIAccess({ sysex: true })` do navegador, trocando mensagens SysEx no formato proprietário do dispositivo — documentado em [`tools/SYSEX_PAYLOAD_FORMAT.md`](tools/SYSEX_PAYLOAD_FORMAT.md).

Resumo do formato de mensagem (BLE):

```
[80][80][F0] [CRC] [Total Pkts] [Pkt Atual] [Tam. Payload] [Payload...] [F7]
```

O cabeçalho `8080` (timestamp BLE) é removido quando a transmissão é via USB.

## Pré-requisitos

- Um navegador com suporte à **Web MIDI API com SysEx** (Chrome ou Edge no desktop). Firefox e Safari não suportam essa API.
- Um pedal **Sonicake Pocket Master** conectado via cabo USB-C ou pareado via Bluetooth LE.

## Como usar

1. Clone o repositório:
   ```bash
   git clone https://github.com/candidopedro/pocket_one.git
   cd pocket_one
   ```
2. Abra o arquivo `index.html` diretamente em um navegador compatível (ou sirva a pasta com um servidor estático simples, ex. `npx serve .`).
3. Conecte o pedal ao computador (USB) ou pareie via Bluetooth.
4. Clique em **Connect** e conceda a permissão de acesso MIDI/SysEx quando solicitado.
5. Navegue pelos presets de fábrica/usuário na barra lateral e edite a cadeia de efeitos no painel central.

## Estrutura do projeto

```
pocket_one/
├── index.html                      # Aplicação principal (UI + lógica + biblioteca de efeitos)
├── style.css                       # Estilos da interface
├── img/
│   └── pedal_main.png              # Imagem do pedal
└── tools/                          # Ferramentas usadas na engenharia reversa do protocolo
    ├── README.md
    ├── SYSEX_PAYLOAD_FORMAT.md     # Documentação completa do protocolo SysEx
    ├── bruteforcetools/            # Ferramentas de brute force de checksum/parâmetros
    ├── commandsequenzeverifier/    # Validação de sequências de comandos no pedal real
    ├── fulldumpanalyzer/           # Extração de nomes de presets a partir de um dump completo
    └── parametercommandanalyzer/   # Análise e geração de comandos de parâmetro a partir de capturas
```

## Ferramentas de engenharia reversa (`tools/`)

Esses utilitários HTML/JS independentes foram criados durante o processo de descoberta do protocolo e são úteis para quem quiser continuar a pesquisa:

| Ferramenta | Função |
|---|---|
| **bruteforcetools** | Descobre por força bruta o checksum (CRC) e valores de parâmetro de comandos, usando Web Bluetooth para testar candidatos direto no pedal e observar o ACK de sucesso. |
| **commandsequenzeverifier** | Envia uma sequência de comandos hexadecimais ao pedal via Web Bluetooth e confirma se cada um foi reconhecido (ACK), com log em tempo real. |
| **fulldumpanalyzer** | Decodifica os nomes de presets (fábrica/usuário) a partir de um dump SysEx completo do dispositivo. |
| **parametercommandanalyzer** | Analisa capturas de comandos "sniffados", identifica módulo/algoritmo/valor de cada um e gera os comandos que estiverem faltando em uma faixa de valores. |

Mais detalhes de cada uma em seus respectivos `README.md`.

Também há uma planilha com o mapeamento de comandos SysEx já identificados, mencionada em [`tools/README.md`](tools/README.md).

## Aviso legal

"Pocket Master" e "Sonicake" são marcas de seus respectivos proprietários. Este projeto é resultado de engenharia reversa feita de forma independente para fins educacionais e de interoperabilidade, sem qualquer afiliação, endosso ou suporte oficial da Sonicake.
