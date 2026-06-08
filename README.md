# Emulação de Infraestrutura Web e Análise Comparativa de Segurança: HTTP vs. HTTPS via Inspeção de Pacotes

Este repositório contém o ambiente de laboratório para emulação de serviços web utilizando containers Docker, com o objetivo de analisar as diferenças estruturais, de transporte e de segurança entre os protocolos **HTTP (Cleartext)** e **HTTPS (TLS Termination)**. A validação e a análise dos dados foram realizadas via inspeção profunda de pacotes (Deep Packet Inspection - DPI) utilizando o analisador de protocolos **Wireshark**.

---

## 1. Arquitetura do Ambiente Emulado

O ambiente foi projetado utilizando isolamento por containers para simular um cenário real de servidor de aplicação exposto. Para garantir a observabilidade do tráfego a partir do Host Sistema Operacional (Windows), o fluxo de dados foi roteado através da interface de loopback nativa.

```text
+---------------------------------------------------------------------------------+
|                                WINDOWS HOST (CLIENT)                            |
|  +------------------+         +---------------------+        +---------------+  |
|  |   Native cURL    | ------> |  Npcap Loopback     | -----> | Wireshark DPI |  |
|  |   (HTTP/HTTPS)   |         |  Pseudo-Interface   |        | (Capture)     |  |
+--+------------------+---------+---------------------+--------+---------------+--+
         |                                 ^
         | Roteamento de Portas            | Interface Bind
         v                                 v
+---------------------------------------------------------------------------------+
|                                DOCKER SUBSYSTEM                                 |
|  +---------------------------------------------------------------------------+  |
|  | Bridge Network (rede-lab)                                                 |  |
|  |                                                                           |  |
|  |   +-------------------------------------------------------------------+   |  |
|  |   | Container: servidor-web (Nginx Reverse Proxy)                     |   |  |
|  |   | - Port 8080 (Ingress) -> Container Port 80 (HTTP Engine)          |   |  |
|  |   | - Port 8443 (Ingress) -> Container Port 443 (TLS Engine)         |   |  |
|  |   +-------------------------------------------------------------------+   |  |
|  +---------------------------------------------------------------------------+  |
+---------------------------------------------------------------------------------+