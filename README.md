# Sistema de Semáforo Inteligente - G03

Aqui está a documentação do projeto feito para testar nossos conhecimentos com ESP32 e Ubidots.

## 📋 Documentação do Projeto

Este repositório contém um sistema de semáforo inteligente que utiliza:
- **2 Semáforos** controlados de forma coordenada
- **ESP32** como microcontrolador
- **Ubidots** para monitoramento IoT

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

### 📄 Template de Documentação

Para facilitar o preenchimento e organização da documentação completa do projeto, utilize o arquivo:

👉 **[TEMPLATE_PROJETO.md](TEMPLATE_PROJETO.md)**

Este template contém seções organizadas com hierarquia visual clara, incluindo:
- Informações do projeto
- Descrição técnica do sistema
- Lógica de funcionamento detalhada
- Especificações de hardware e software
- Instalação e configuração
- Testes e validação
- E muito mais!

### 🚀 Como Usar

1. Abra o arquivo `TEMPLATE_PROJETO.md`
2. Preencha as seções marcadas com `[Preencher...]`
3. Substitua os exemplos pelos dados reais do seu projeto
4. Mantenha a estrutura e hierarquia do documento

---

**Equipe:** G03  
**Tecnologias:** ESP32, Ubidots, Arduino IDE/PlatformIO
