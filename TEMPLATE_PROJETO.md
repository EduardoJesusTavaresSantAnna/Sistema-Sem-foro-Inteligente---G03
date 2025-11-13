# Sistema de Semáforo Inteligente - G03

## 1. INFORMAÇÕES DO PROJETO

### 1.1 Identificação
- **Nome do Projeto:** [Preencher com o nome completo do projeto]
- **Equipe:** [Listar membros da equipe]
- **Data de Início:** [DD/MM/AAAA]
- **Última Atualização:** [DD/MM/AAAA]
- **Versão:** [X.X.X]

### 1.2 Objetivo Geral
[Descrever o objetivo principal do projeto de forma clara e objetiva]

### 1.3 Objetivos Específicos
- [Objetivo específico 1]
- [Objetivo específico 2]
- [Objetivo específico 3]

---

## 2. DESCRIÇÃO DO SISTEMA

### 2.1 Visão Geral
[Descrever resumidamente como o sistema de semáforo inteligente funciona]

### 2.2 Componentes do Sistema
- **Semáforo 1:** [Descrever função e localização]
- **Semáforo 2:** [Descrever função e localização]
- **Controlador:** [Especificar hardware utilizado - ex: ESP32]
- **Plataforma de Monitoramento:** [Especificar - ex: Ubidots]
- **Sensores:** [Listar sensores utilizados, se houver]

---

## 3. LÓGICA DE FUNCIONAMENTO

### 3.1 Estados dos Semáforos
O sistema opera com dois semáforos que trabalham de forma coordenada e mutuamente exclusiva.

#### 3.1.1 Funcionamento Simultâneo
- **Quando Semáforo 1 está VERDE** → Semáforo 2 está VERMELHO
- **Quando Semáforo 2 está VERDE** → Semáforo 1 está VERMELHO

#### 3.1.2 Sequência de Transição
A mudança entre estados segue uma sequência específica para garantir segurança:

**Transição do Semáforo 1 (Verde → Vermelho):**
1. Semáforo 1: VERDE
2. Semáforo 1: AMARELO (aguarda tempo de transição)
3. Semáforo 1: VERMELHO
4. **Somente após** o Semáforo 1 chegar ao VERMELHO → Semáforo 2: VERDE

**Transição do Semáforo 2 (Verde → Vermelho):**
1. Semáforo 2: VERDE
2. Semáforo 2: AMARELO (aguarda tempo de transição)
3. Semáforo 2: VERMELHO
4. **Somente após** o Semáforo 2 chegar ao VERMELHO → Semáforo 1: VERDE

### 3.2 Temporização
- **Tempo de VERDE:** [X segundos]
- **Tempo de AMARELO:** [X segundos]
- **Tempo de VERMELHO:** [X segundos]

### 3.3 Diagrama de Estados
```
[Inserir diagrama ou fluxograma mostrando as transições entre estados]
```

---

## 4. ESPECIFICAÇÕES TÉCNICAS

### 4.1 Hardware
#### 4.1.1 Microcontrolador
- **Modelo:** [ex: ESP32 DevKit]
- **Tensão de Operação:** [ex: 3.3V/5V]
- **Portas Utilizadas:** [Listar GPIO utilizadas]

#### 4.1.2 LEDs
- **LED Verde (Semáforo 1):** GPIO [X]
- **LED Amarelo (Semáforo 1):** GPIO [X]
- **LED Vermelho (Semáforo 1):** GPIO [X]
- **LED Verde (Semáforo 2):** GPIO [X]
- **LED Amarelo (Semáforo 2):** GPIO [X]
- **LED Vermelho (Semáforo 2):** GPIO [X]

#### 4.1.3 Resistores
- **Valor dos Resistores:** [ex: 220Ω]
- **Quantidade:** [X unidades]

#### 4.1.4 Outros Componentes
- [Listar outros componentes utilizados]

### 4.2 Software
- **Linguagem de Programação:** [ex: C++ para Arduino/ESP32]
- **IDE Utilizada:** [ex: Arduino IDE, PlatformIO]
- **Bibliotecas:** [Listar bibliotecas necessárias]
- **Versão do Firmware:** [X.X.X]

### 4.3 Conectividade
- **Protocolo de Comunicação:** [ex: Wi-Fi, MQTT]
- **Plataforma IoT:** [ex: Ubidots]
- **Credenciais de Rede:** [Instruções para configuração]

---

## 5. INSTALAÇÃO E CONFIGURAÇÃO

### 5.1 Requisitos
- [Listar requisitos de hardware]
- [Listar requisitos de software]
- [Listar dependências]

### 5.2 Montagem do Circuito
1. [Passo a passo para montagem]
2. [Incluir esquema elétrico se disponível]
3. [Precauções de segurança]

### 5.3 Configuração do Software
```
[Instruções de configuração passo a passo]
```

### 5.4 Upload do Código
```
[Instruções para compilar e fazer upload do código]
```

---

## 6. OPERAÇÃO DO SISTEMA

### 6.1 Inicialização
[Descrever o processo de inicialização do sistema]

### 6.2 Modos de Operação
- **Modo Automático:** [Descrever funcionamento]
- **Modo Manual:** [Descrever funcionamento, se aplicável]
- **Modo de Teste:** [Descrever funcionamento, se aplicável]

### 6.3 Monitoramento
[Descrever como monitorar o sistema via Ubidots ou outra plataforma]

---

## 7. CÓDIGO-FONTE

### 7.1 Estrutura do Código
```
[Descrever a estrutura/organização do código]
```

### 7.2 Funções Principais
- **`setup()`:** [Descrição]
- **`loop()`:** [Descrição]
- **`alternarSemaforo()`:** [Descrição]
- **`transicaoSemaforo()`:** [Descrição]
- [Listar outras funções importantes]

### 7.3 Variáveis Globais
- [Listar e descrever variáveis importantes]

---

## 8. TESTES E VALIDAÇÃO

### 8.1 Testes Realizados
- [ ] Teste de inicialização
- [ ] Teste de transição Semáforo 1 → Semáforo 2
- [ ] Teste de transição Semáforo 2 → Semáforo 1
- [ ] Teste de temporização
- [ ] Teste de conectividade IoT
- [ ] Teste de longa duração

### 8.2 Resultados
[Descrever resultados dos testes]

### 8.3 Problemas Identificados
- [Listar problemas encontrados]
- [Soluções implementadas]

---

## 9. MANUTENÇÃO

### 9.1 Manutenção Preventiva
- [Procedimentos de manutenção regular]

### 9.2 Troubleshooting
| Problema | Causa Possível | Solução |
|----------|----------------|---------|
| [Problema 1] | [Causa] | [Solução] |
| [Problema 2] | [Causa] | [Solução] |
| [Problema 3] | [Causa] | [Solução] |

---

## 10. MELHORIAS FUTURAS

### 10.1 Funcionalidades Planejadas
- [Funcionalidade 1]
- [Funcionalidade 2]
- [Funcionalidade 3]

### 10.2 Otimizações
- [Otimização 1]
- [Otimização 2]

---

## 11. REFERÊNCIAS

### 11.1 Documentação Técnica
- [Datasheet do ESP32]
- [Documentação do Ubidots]
- [Outras referências]

### 11.2 Bibliografia
- [Artigos e livros consultados]

### 11.3 Links Úteis
- [Links para recursos online]

---

## 12. ANEXOS

### 12.1 Esquema Elétrico
[Inserir imagem do esquema elétrico]

### 12.2 Fotos do Projeto
[Inserir fotos da montagem física]

### 12.3 Código Completo
[Link para arquivo do código ou inserir código aqui]

### 12.4 Vídeos Demonstrativos
[Links para vídeos do sistema em funcionamento]

---

## 13. CONTATO E SUPORTE

- **Equipe:** [Email de contato]
- **Repositório:** [Link do GitHub]
- **Documentação Online:** [Link se disponível]

---

**Nota:** Este documento deve ser atualizado regularmente conforme o projeto evolui. Mantenha todas as seções preenchidas e atualizadas para facilitar a compreensão e manutenção do sistema.
