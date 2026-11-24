# 🏃‍♂️ Pé de Chumbo • Lab PPG (Monitor Fisiológico Web)

Aplicação web leve e responsiva desenvolvida para a **Equipe de Corrida Pé de Chumbo**, capaz de medir e interpretar parâmetros cardiovasculares e respiratórios diretamente pelo navegador do celular utilizando apenas a **câmera traseira e o flash LED** (Fotopletismografia — PPG).

Todo o processamento digital de sinais (DSP) ocorre **100% localmente no navegador do atleta**, sem necessidade de instalação de aplicativos nativos, sem backend e sem envio de imagens ou dados biométricos para servidores externos.

---

## 📋 Protocolos e Funcionalidades

O sistema conta com **4 protocolos esportivos guiados**, fluxo rápido para avaliações em grupo e interpretação automática dos resultados:

| Protocolo | Duração | Métricas Extraídas | Aplicação no Treino |
| :--- | :---: | :--- | :--- |
| **Prontidão & VFC** | `30s` | FC Média (BPM), VFC (RMSSD), Perfusão (PI%) | Avaliar recuperação autonômica antes de treinos intensos ou provas. |
| **Recuperação HRR 60s** | `60s` | FC Inicial, FC Final e Queda ($\Delta$ BPM) | Medir a velocidade de recuperação cardíaca no 1º minuto pós-tiro ou chegada. |
| **Respiração RPM** | `45s` | Ciclos respiratórios por minuto (RPM) | Auxiliar no controle respiratório e volta à calma no desaquecimento. |
| **Monitor Livre** | `Contínuo` | BPM, RMSSD, RPM e PI% em tempo real | Acompanhamento livre e visualização da onda de pulso (PPG). |

### Recursos para Uso em Grupo
* **Modo Equipe:** Cadastro rápido de atletas na sessão com atalhos de seleção e botão **"Próximo Atleta da Equipe"** para avaliações em sequência.
* **Laudo Esportivo Explicado:** Ao término de cada leitura, o sistema classifica os índices (ex.: *Alta prontidão*, *Sinal de fadiga*, *Excelente recuperação*) e explica o significado prático para o corredor.
* **Guia Didático Integrado:** Aba dedicada (*"O que Mede?"*) explicando a fisiologia por trás de cada indicador e suas faixas de referência.
* **Compartilhamento e Exportação:** Cópia formatada para envio no WhatsApp (individual ou resumo do grupo) e exportação do histórico completo em planilha **CSV**.

---

## ⚙️ Arquitetura e Processamento de Sinal (DSP)

O algoritmo implementado em JavaScript puro (`index.html`) executa as seguintes etapas em tempo real:

1. **Captação Óptica (Canal Verde Invertido):** Utiliza a média do canal verde (`-avgG`) recortada do centro do frame (`60x60` px). Sob o flash LED encostado na pele, o canal verde evita a saturação comum do canal vermelho e maximiza o contraste da absorção da hemoglobina.
2. **Base Temporal Precisa:** Todas as janelas de filtragem operam em milissegundos via `performance.now()`, compensando variações de taxa de quadros (`30–60 FPS`) entre diferentes smartphones.
3. **Filtragem Passa-Banda:**
   * *Passa-Alta (janela de 750 ms):* Remove oscilações lentas de pressão do dedo e movimento respiratório para isolar a onda sistólica.
   * *Passa-Baixa (janela de 120 ms):* Suaviza ruídos de alta frequência do sensor CMOS.
4. **Cálculo de Métricas Fisiológicas:**
   * **BPM:** Derivado da mediana móvel dos últimos 8 intervalos RR válidos (`300 ms` a `1500 ms`).
   * **VFC (RMSSD):** Raiz quadrada da média das diferenças sucessivas entre intervalos RR consecutivos, com filtro de rejeição de artefatos (`±30%` da mediana).
   * **Frequência Respiratória (RPM):** Extraída da modulação lenta da linha de base (`0,14 Hz` a `0,45 Hz`) gerada pela Arritmia Sinusal Respiratória.
   * **Índice de Perfusão (PI%):** Razão percentual entre a amplitude pulsátil ($\text{AC}$) e a luminosidade basal ($\text{DC}$).

---

## 📱 Como Utilizar Corretamente

1. **Acesso via HTTPS:** Por política de segurança dos navegadores modernos, o acesso à câmera exige conexão segura (`https://`).
2. **Posicionamento do Dedo:**
   * Toque em **Iniciar Medição**.
   * Apoie a ponta do dedo indicador cobrindo simultaneamente a **lente da câmera traseira principal e o flash LED**.
   * **Não pressione com força:** apertar demais comprime os capilares sanguíneos e achata a onda de pulso. Encoste apenas o suficiente para vedar a luz externa.
3. **Compatibilidade:**
   * **Android (Chrome / Edge / Samsung Internet):** Acionamento automático do flash traseiro (`torch: true`) e bloqueio de suspensão de tela (`Wake Lock API`).
   * **iOS (Safari):** O WebKit não permite acionar o flash traseiro via navegador; utilize uma fonte de luz externa próxima ao dedo durante a leitura.

---

## 🚀 Publicação no GitHub Pages

1. Faça o upload do arquivo `index.html` na raiz deste repositório.
2. Acesse **Settings** > **Pages**.
3. Em **Build and deployment**, selecione **Deploy from a branch**, escolha a branch `main` e a pasta `/ (root)` e clique em **Save**.
4. Acesse a URL HTTPS gerada pelo GitHub diretamente no navegador do celular.

---

## ⚠️ Aviso Importante

Esta ferramenta possui caráter **exclusivamente educativo e de acompanhamento esportivo recreativo**. Os valores exibidos não substituem oxímetros de pulso, frequencímetros cardíacos certificados ou avaliação médica profissional. O aplicativo não realiza medição de pressão arterial.
