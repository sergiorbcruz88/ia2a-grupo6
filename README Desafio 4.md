# Agente de IA para Cálculo de Vale-Refeição (VR)

## 1. Visão Geral

Este projeto utiliza um agente de Inteligência Artificial (IA) para automatizar e enriquecer o processo de cálculo mensal do Vale-Refeição (VR). O notebook processa múltiplas fontes de dados de RH (ativos, desligados, férias, etc.), aplica regras de negócio complexas para determinar a elegibilidade dos funcionários e consulta um modelo de linguagem (LLM) para obter os valores diários de VR com base nas convenções coletivas de cada sindicato e cargo.

O objetivo é gerar uma planilha final (`VR_PROCESSADO_FINAL.xlsx`) pronta para ser enviada ao financeiro, com todos os cálculos de custo e desconto já realizados.

## 2. Pré-requisitos

Antes de executar este notebook, certifique-se de que os seguintes pré-requisitos foram atendidos:

* **Acesso ao Google Colab:** O projeto foi desenhado para ser executado no ambiente do Google Colab.
* **Chave de API:** Você precisa de uma chave de API de um provedor de modelos de linguagem compatível com a API da OpenAI (neste caso, OpenRouter).
* **Arquivos de Dados:** Todos os arquivos Excel de entrada devem estar presentes no mesmo diretório do notebook. A lista de arquivos necessários é:
    * `ADMISSÃO ABRIL.xlsx`
    * `AFASTAMENTOS.xlsx`
    * `APRENDIZ.xlsx`
    * `ATIVOS.xlsx`
    * `Base sindicato x valor.xlsx`
    * `DESLIGADOS.xlsx`
    * `ESTÁGIO.xlsx`
    * `EXTERIOR.xlsx`
    * `FÉRIAS.xlsx`

## 3. Configuração do Ambiente

Siga estes passos para configurar o ambiente antes da primeira execução:

1.  **Faça o Upload dos Arquivos:** Use a funcionalidade de "Arquivos" no painel esquerdo do Google Colab para fazer o upload de todos os arquivos Excel listados acima.
2.  **Configure a Chave de API (Secret):**
    * Clique no ícone de **chave (🔑)** no menu esquerdo do Colab para abrir o painel de "Secrets".
    * Adicione um novo "secret" com o **Nome** `OPENROUTER_API_KE`.
    * No campo **Valor**, cole a sua chave de API.
    * Certifique-se de que a opção **"Notebook access"** esteja marcada.

## 4. Estrutura do Notebook

O processo é dividido em três células principais:

* **Célula 1: Setup do Ambiente e Carregamento de Dados:** Esta célula instala as bibliotecas, importa os módulos, configura o sistema de logs, estabelece a conexão com a API da IA e carrega todos os arquivos Excel brutos para a memória em `DataFrames` do `pandas`.

* **Célula 2: Definição da Arquitetura do Agente:** O coração do projeto. Esta célula define a classe `VRAgent`, que encapsula toda a lógica de negócio. A lógica inclui:
    * Padronização de colunas.
    * Aplicação de regras de exclusão (ex: diretores, estagiários).
    * **Consulta em Lote à IA:** Consolida todas as combinações únicas de `estado` e `cargo` e faz uma **única chamada** à API para obter os valores diários de VR, evitando erros de "Too Many Requests".
    * Enriquecimento dos dados e aplicação individual dos valores de VR.
    * Cálculo final dos dias pagáveis e dos valores totais.

* **Célula 3: Configuração e Execução da Missão:** Esta é a célula de controle. Aqui você define os parâmetros da execução (ano, mês, feriados), instancia o agente e chama o método `.run()` para executar todo o fluxo. Ao final, ela exibe uma amostra do resultado e um relatório de validação.

## 5. Como Executar

1.  Certifique-se de que a **Configuração do Ambiente** (passo 3) foi concluída.
2.  Execute as células em ordem, de 1 a 3.
3.  Aguarde a conclusão da Célula 3. O processo pode levar alguns minutos, especialmente durante a consulta à IA. Acompanhe o progresso através dos logs.
4.  Ao final da execução, um arquivo chamado `VR_PROCESSADO_FINAL.xlsx` será gerado e aparecerá no seu painel de arquivos. Você pode fazer o download deste arquivo.
