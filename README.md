# Sistema de Semáforo Inteligente - G03
## Membros do grupo: Eduardo Jesus, Guilherme Schmidt, João Agmont, Leonardo Corbi, Lucas Lopez, Lucas Pomin

Aqui está a documentação do projeto feito para testar nossos conhecimentos com ESP32 e Ubidots.

## 📋 Documentação do Projeto

Este repositório contém um sistema de semáforo inteligente que utiliza:
- **2 Semáforos** controlados de forma coordenada
- **ESP32** como microcontrolador
  
### 🎯 Lógica de Funcionamento

O sistema opera com dois semáforos que trabalham de forma sincronizada:

1. **Operação Coordenada:**
   - Quando o Semáforo 1 está **VERDE** → Semáforo 2 está **VERMELHO**
   - Quando o Semáforo 2 está **VERDE** → Semáforo 1 está **VERMELHO**

2. **Sequência de Transição Segura:**
   - Semáforo com luz verde → muda para **AMARELO**
   - Aguarda tempo de transição
   - Muda para **VERMELHO**
   - Somente após ficar vermelho, o outro semáforo acende o **VERDE**
